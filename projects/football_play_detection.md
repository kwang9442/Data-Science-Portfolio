<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D1 Football — Play Detection Project</title>

    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <link rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/default.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>

    <style>
        body {
            font-family: Inter, sans-serif;
            margin: 0;
            padding: 0;
            background: #f5f6fa;
            color: #222;
        }
        header {
            background: #0b1d3a;
            color: white;
            padding: 45px 20px;
            text-align: center;
        }
        header h1 {
            margin: 0;
            font-size: 2.4rem;
            font-weight: 700;
        }
        .container {
            max-width: 900px;
            margin: 40px auto;
            background: white;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }
        h2 {
            margin-top: 40px;
            font-size: 1.7rem;
            color: #0b1d3a;
        }
        .section-desc {
            font-size: 1.05rem;
            margin-bottom: 15px;
            color: #333;
        }
        pre {
            background: #1e1e1e;
            padding: 15px;
            border-radius: 8px;
            overflow-x: auto;
            color: #eee;
        }
        .disclaimer {
            background: #fff3cd;
            padding: 12px;
            border-left: 5px solid #e0b400;
            border-radius: 6px;
            margin-top: 15px;
            font-size: 0.95rem;
        }
        footer {
            text-align: center;
            padding: 20px;
            color: #555;
            margin-top: 30px;
        }
    </style>
</head>

<body>

<header>
    <h1>D1 Football — Play Detection Project</h1>
    <p>Velocity Segmentation • Change-Point Detection • Clustering</p>
</header>

<div class="container">

    <h2>Project Overview</h2>
    <p>
        This project builds a complete analytics pipeline for NCAA D1 football athlete tracking data.  
        The goal is to automatically identify individual plays, characterize movement intensity, detect 
        structural changes in velocity profiles, and group similar movement patterns.
    </p>

    <div class="disclaimer">
        <strong>Confidentiality Notice:</strong><br>
        Due to NCAA Division 1 data security policies, raw tracking datasets, velocity distributions, 
        and athlete-identifying information cannot be displayed. All code examples shown below operate 
        on anonymized or synthetic structures for demonstration.
    </div>

    <h2>1. Peak-Based Play Segmentation</h2>
    <p class="section-desc">
        This function identifies play boundaries using velocity peaks, then expands each segment while 
        allowing for small pauses. It forms the backbone of play detection.
    </p>

<pre><code class="language-python">
def detect_play_segments(df, high_vel=2.0, low_vel=0.5, min_duration=0.5, max_pause=1.5):
    """
    Detect play intervals based on velocity peaks and expanded boundaries.
    """

    df = df.sort_values("time").reset_index(drop=True)
    segments = []
    i = 0

    while i < len(df):
        v = df.loc[i, "velocity"]

        # Detect start of velocity peak
        if v >= high_vel:
            peak_start = i
            while i < len(df) and df.loc[i, "velocity"] >= high_vel:
                i += 1
            peak_end = i - 1

            # Expand left/downward
            start_idx = peak_start
            while start_idx > 0 and df.loc[start_idx, "velocity"] > low_vel:
                start_idx -= 1

            # Expand right/upward
            end_idx = peak_end
            pause = 0

            while end_idx < len(df) - 1:
                next_v = df.loc[end_idx + 1, "velocity"]
                dt = df.loc[end_idx + 1, "time"] - df.loc[end_idx, "time"]

                if next_v > low_vel:
                    pause = 0
                    end_idx += 1
                else:
                    pause += dt
                    if pause <= max_pause:
                        end_idx += 1
                    else:
                        break

            seg_start = df.loc[start_idx, "time"]
            seg_end = df.loc[end_idx, "time"]
            duration = seg_end - seg_start

            if duration >= min_duration:
                segments.append((seg_start, seg_end, duration))

            i = end_idx
        else:
            i += 1

    return segments
</code></pre>

    <h2>2. Abrupt Movement Change Detection</h2>
    <p class="section-desc">
        Identifies sudden changes in velocity or position that often align with transitions, cuts, 
        and start/stop events. Used to refine play boundaries.
    </p>

<pre><code class="language-python">
def detect_abrupt_changes(df, pct=0.20, suppress_window=3):
    """
    Detect abrupt changes in velocity or XY position.
    """

    dv = df['velocity'].diff().abs()
    dx = df['x'].diff().abs()
    dy = df['y'].diff().abs()

    thresh_v = dv.max() * pct
    thresh_x = dx.max() * pct
    thresh_y = dy.max() * pct

    abrupt = pd.Series(False, index=df.index)

    for i in range(1, len(df)):
        if (dv.iloc[i] >= thresh_v or
            dx.iloc[i] >= thresh_x or
            dy.iloc[i] >= thresh_y):

            abrupt.iloc[i] = True

            # Suppress subsequent points so we don't double-count
            for k in range(1, suppress_window + 1):
                if i + k < len(df):
                    abrupt.iloc[i + k] = False

    return abrupt
</code></pre>


    <h2>3. Mapping Plays to Practice Periods (IntervalTree)</h2>
    <p class="section-desc">
        Each athlete’s play is matched to coaching-defined practice periods using an efficient 
        interval tree lookup. This enables context labeling and evaluation.
    </p>

<pre><code class="language-python">
from intervaltree import IntervalTree

# Build lookup trees
trees = {}
for athlete, grp in periods.groupby('athlete_id'):
    tree = IntervalTree()
    for _, row in grp.iterrows():
        if row['start_time'] != row['end_time']:
            tree.addi(row['start_time'], row['end_time'], row['period_name'])
    trees[athlete] = tree

def map_to_period(row):
    athlete = row['athlete_id']
    timestamp = row['time']

    if athlete in trees:
        matches = trees[athlete][timestamp]
        if matches:
            return list(matches)[0].data
    
    return pd.NA
</code></pre>


    <h2>4. Change-Point Detection (PELT)</h2>
    <p class="section-desc">
        PELT identifies structural changes in the velocity signal — typically acceleration, deceleration,  
        or role transitions. Useful for validating segmentation quality.
    </p>

<pre><code class="language-python">
def detect_change_points(df, label):
    """
    Detect velocity changepoints using the PELT algorithm.
    """
    vel = df['velocity'].values
    algo = rpt.Pelt(model="rbf").fit(vel)
    cps = algo.predict(pen=30)

    plt.figure(figsize=(12, 4))
    plt.plot(df['time'], vel, label=label)

    for cp in cps[:-1]:
        plt.axvline(x=df['time'].iloc[cp], color='r', linestyle='--')

    plt.title(f'Changepoints in Velocity — {label}')
    plt.xlabel('Time')
    plt.ylabel('Velocity')
    plt.grid(True)
    plt.legend()
    plt.show()
</code></pre>


    <h2>5. Clustering Player Trajectories</h2>
    <p class="section-desc">
        Play trajectories are standardized, flattened, and clustered using K-Means to identify 
        repeated movement patterns (routes, position habits, drill types).
    </p>

<pre><code class="language-python">
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

trajectory_list = []

for start, end, _ in segments:
    traj = df[(df['time'] >= start) & (df['time'] <= end)][['x', 'y']]

    if len(traj) >= 5:
        traj_resampled = traj.reset_index(drop=True).interpolate().iloc[:35]
        trajectory_list.append(traj_resampled.values.flatten())

# Build clustering matrix
X = np.vstack(trajectory_list)
X = StandardScaler().fit_transform(X)

kmeans = KMeans(n_clusters=3, random_state=0)
labels = kmeans.fit_predict(X)

# Visualization
for i, traj in enumerate(trajectory_list):
    coords = traj.reshape(-1, 2)
    plt.plot(coords[:, 0], coords[:, 1], label=f"Cluster {labels[i]}", alpha=0.6)
</code></pre>

</div>

<footer>
    © 2025 | Data Science Portfolio — Kailani Wang
</footer>

<script>hljs.highlightAll();</script>

</body>
</html>
