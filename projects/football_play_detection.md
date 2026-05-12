<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D1 Football — Tracking Data Analytics System</title>

    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: Inter, sans-serif;
            margin: 0;
            background: #f5f6fa;
            color: #222;
        }

        header {
            background: #0b1d3a;
            color: white;
            padding: 60px 20px;
            text-align: center;
        }

        header h1 {
            margin: 0;
            font-size: 2.6rem;
        }

        .container {
            max-width: 950px;
            margin: 40px auto;
            background: white;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }

        h2 {
            margin-top: 40px;
            color: #0b1d3a;
        }

        p {
            line-height: 1.6;
        }

        .pipeline {
            background: #f1f5f9;
            padding: 15px;
            border-left: 4px solid #0b1d3a;
            border-radius: 6px;
            margin-top: 10px;
        }

        pre {
            background: #1e1e1e;
            color: #eee;
            padding: 12px;
            border-radius: 8px;
            overflow-x: auto;
        }

        pre code {
            color: #7aa2f7;
        }

        .note {
            background: #fff3cd;
            padding: 12px;
            border-left: 4px solid #e0b400;
            border-radius: 6px;
            margin-top: 20px;
            font-size: 0.95rem;
        }

        footer {
            text-align: center;
            padding: 20px;
            color: #555;
        }
    </style>
</head>

<body>

<header>
    <h1>Football Tracking Data Analytics System</h1>
    <p>GPS Tracking • Play Detection • Movement Modeling • Clustering</p>
</header>

<div class="container">

    <h2>Project Overview</h2>

    <p>
        This project built an end-to-end analytics system for NCAA Division I football GPS tracking data.
        It transformed raw athlete movement data (x, y position + velocity over time) into structured “plays”
        that could be used to analyze workload, movement intensity, and behaviors.
    </p>

    <p>
        Starting from raw sensor data, I built a full pipeline including data cleaning, signal processing,
        play segmentation, event detection, and clustering of movement patterns.
    </p>

    <h2>End-to-End Pipeline</h2>

    <div class="pipeline">
        Raw GPS Data → Cleaning & Filtering → Velocity Signal Processing → Play Detection → 
        Change-Point Validation → Period Mapping → Trajectory Clustering → Output Plays
    </div>

    <h2>1. Data Processing & Preperation</h2>

    <p>
        The raw dataset consists of GPS tracking data containing player position (x, y),
        velocity, acceleration, timestamps, etc.
    </p>

    <p>
        Preprocessing steps included:
    </p>

    <ul>
        <li>Filtered individual athletes from team tracking streams</li>
        <li>Sorted and aligned time-series data</li>
        <li>Handled missing or noisy velocity values</li>
        <li>Standardized timestamps across drills</li>
    </ul>

    <h2>2. Movement Signal Processing</h2>

    <p>
        This velocity delta signal became a key feature for detecting transitions between movement states (rest → acceleration → active     play).
    </p>

    <pre><code>
df['dv'] = df['v'].diff().abs()
    </code></pre>

    <p>
        This distribution was tracking one player across the time interval of one drill ran at practice. Velocity change signals (dv) were computed from raw GPS velocity data to identify meaningful movement transitions.
        Threshold-based filtering was used to isolate high-intensity changes while reducing noise from small fluctuations.
        Green points represent movement events that exceeded adaptive percentile thresholds.
    </p>

<div style="text-align: center;">
        <img src="../images/Velocity-Changes-00A-Flex.png" alt="Detected Plays Intervals"  width="600">
</div>

    <h2>3. Play Detection Engine (Time-Series Segmentation)</h2>

    <p>
        A custom rule-based segmentation system was made to detect discrete “plays” from continuous movement data.
        A play is defined as a bounded time interval where a players' velocity exceeds a dynamic threshold and then returns to baseline.
    </p>
    
    <p>
        The algorithm uses both:
    </p>
    
    <ul>
        <li>Velocity thresholding (high-intensity movement detection)</li>
        <li>Temporal continuity constraints (preventing false splits)</li>
        <li>Pause-time tolerance for directional changes</li>
    </ul>
    
    <pre><code>
    if v >= high_thresh:
        peak_start = i
    
    while next_v > low_thresh:
        end_idx += 1
    </code></pre>
    
    <p>
            This approach ensures that each detected segment shows a physically meaningful play rather than short noise spikes in sensor data. Detected play segments are highlighted in red. Plays were identified by locating sustained high-velocity movement periods bounded by lower activity thresholds and pause-tolerant segmentation rules.
    </p>

    <div style="display: flex; justify-content: center; gap: 20px;">
        <img src="../images/Detected-High-Peaks.png" style="width: 45%;" alt="Velocity Changes for One Drill">
        <img src="../images/Specific-Detection.png" style="width: 45%;" alt="Specific Detection">
    </div>

   <h2>4. Change-Point Detection (Signal Validation)</h2>

<p>
    To validate rule-based segmentation, I implemented statistical change-point detection using
    the PELT algorithm with an RBF kernel to detect structural shifts in velocity time-series data.
</p>

<pre><code>
algo = rpt.Pelt(model="rbf").fit(vel)
change_points = algo.predict(pen=30)
</code></pre>

<p>
    This allowed cross-validation between heuristic play detection and statistically inferred motion changes,improving segmentation reliability. Statistical change-point detection was applied to player velocity signals using the PELT algorithm. Red dashed lines indicate detected structural shifts in movement intensity, helping validate play segmentation boundaries.
</p>

<div style="text-align: center;">
        <img src="../images/Detected-00A-Flex.png" alt="Detected Velocity 1 Drill"  width="600">
</div>

    <h2>5. Interval Tree Based Segmentation</h2>

<p>
    Interval trees were used to efficiently map GPS timestamps to drill and practice periods,
    enabling fast lookup of contextual metadata for each athlete movement event.
</p>
    
    <pre><code>
    tree.addi(start, end, period_name)
    matches = trees[athlete][time]
    </code></pre>

    <h2>6. Movement Pattern Clustering</h2>

<p>
    To analyze behavioral patterns across time windows, I aggregated 5-second movement chunks and extracted features such as:
    average velocity change and frequency of high-intensity movement events.
</p>

<pre><code>
kmeans = KMeans(n_clusters=3, random_state=42)
labels = kmeans.fit_predict(X)
</code></pre>

<p>
    Five-second movement windows were clustered using KMeans based on average velocity change and frequency of high-intensity movement events. This made three interpretable movement states:
</p>

<ul>
    <li><b>Low Activity:</b> Resting, walking, recovery phases</li>
    <li><b>Moderate Activity:</b> Repositioning and transitional movement</li>
    <li><b>High Activity:</b> Sprinting, cutting, and game-speed exertion</li>
</ul>

<div style="text-align: center;">
        <img src="../images/Movement-Cluster.png" alt="Movement Clusters"  width="540">
</div>
   <h2>What This Project Demonstrates</h2>

<ul>
    <li>Built a full pipeline for processing football tracking data.</li>
    <li>Created movement features from raw GPS sensor data.</li>
    <li>Combined threshold methods with statistical modeling for play detection.</li>
    <li>Applied change-point detection to noisy player movement signals.</li>
    <li>Used KMeans clustering to identify movement and behavior patterns.</li>
</ul>

</div>

<footer>
    © 2025 | Data Science Portfolio — Kailani Wang
</footer>

</body>
</html>
