<!DOCTYPE html>
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

    <!-- INTRO -->
    <h2>Project Overview</h2>

    <p>
        This project builds an end-to-end analytics system for NCAA Division I football GPS tracking data.
        It transforms raw athlete movement data (x, y position + velocity over time) into structured “plays”
        that can be used to analyze workload, movement intensity, and tactical behavior.
    </p>

    <p>
        Starting from raw sensor data, I built a full pipeline including data cleaning, signal processing,
        play segmentation, event detection, and clustering of movement patterns.
    </p>

    <div class="note">
        <b>Data Note:</b> Due to NCAA data privacy restrictions, raw datasets, athlete identifiers,
        and full implementation details cannot be publicly shared. This project reflects a production-level
        analytics pipeline used in performance analysis workflows.
    </div>

    <!-- PIPELINE -->
    <h2>End-to-End Pipeline</h2>

    <div class="pipeline">
        Raw GPS Data → Cleaning & Filtering → Velocity Signal Processing → Play Detection → 
        Change-Point Validation → Period Mapping → Trajectory Clustering → Output Plays
    </div>

    <!-- DATA CLEANING -->
    <h2>1. Data Processing & Preparation</h2>

    <p>
        The raw dataset consists of 100Hz GPS tracking data containing player position (x, y),
        velocity, acceleration, and timestamps.
    </p>

    <p>
        Preprocessing steps included:
    </p>

    <ul>
        <li>Filtering individual athletes from team tracking streams</li>
        <li>Sorting and aligning time-series data</li>
        <li>Handling missing velocity values</li>
        <li>Standardizing timestamps across drills</li>
    </ul>

    <!-- SIGNAL -->
    <h2>2. Movement Signal Processing</h2>

    <p>
        I computed velocity change (dv) as the primary signal for detecting movement intensity shifts.
    </p>

    <pre>
df['dv'] = df['v'].diff().abs()
    </pre>

    <!-- PLAY DETECTION -->
    <h2>3. Play Detection Engine</h2>

    <p>
        Plays are defined as continuous periods of high-intensity movement separated by low-movement phases.
        A threshold-based system is used with expansion logic to capture full movement sequences.
    </p>

    <pre>
if v >= high_thresh:
    peak_start = i

while next_v > low_thresh:
    end_idx += 1
    </pre>

    <p>
        This ensures natural movement sequences are not split prematurely while still isolating meaningful play events.
    </p>

    <!-- CHANGE POINTS -->
    <h2>4. Structural Validation (Change-Point Detection)</h2>

    <p>
        PELT change-point detection is used to validate whether detected plays align with structural shifts in movement intensity.
    </p>

    <pre>
algo = rpt.Pelt(model="rbf").fit(vel)
cps = algo.predict(pen=30)
    </pre>

    <!-- INTERVAL TREE -->
    <h2>5. Contextual Mapping (Drill & Practice Segments)</h2>

    <p>
        Each detected play is mapped to coaching-defined periods using interval trees.
        This allows performance to be analyzed within specific drills and practice contexts.
    </p>

    <pre>
tree.addi(start, end, period_name)
matches = trees[athlete][time]
    </pre>

    <!-- CLUSTERING -->
    <h2>6. Movement Pattern Clustering</h2>

    <p>
        Plays are clustered using K-Means based on spatial movement (x, y trajectories)
        to identify recurring behavioral patterns.
    </p>

    <pre>
kmeans = KMeans(n_clusters=3, random_state=42)
labels = kmeans.fit_predict(X)
    </pre>

    <ul>
        <li>Cluster 1: low-intensity recovery movement</li>
        <li>Cluster 2: moderate repositioning phases</li>
        <li>Cluster 3: high-intensity sprint/cut actions</li>
    </ul>

    <!-- OUTPUT -->
    <h2>Final Output</h2>

    <p>
        The system outputs structured play-level data containing:
    </p>

    <ul>
        <li>Play start and end times</li>
        <li>Duration and intensity features</li>
        <li>Athlete and drill context</li>
        <li>Movement-based clustering labels</li>
    </ul>

    <p>
        This enables downstream analysis of workload, performance trends, and movement efficiency across athletes and sessions.
    </p>

</div>

<footer>
    © 2025 | Data Science Portfolio — Kailani Wang
</footer>

</body>
</html>
