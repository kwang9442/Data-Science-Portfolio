<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D1 Volleyball — CMJ Load & Readiness Analysis</title>

    <!-- Google Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <!-- Syntax Highlighting -->
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
            padding: 40px 20px;
            text-align: center;
        }
        header h1 {
            margin: 0;
            font-size: 2.3rem;
            font-weight: 700;
        }
        header p {
            margin-top: 10px;
            font-size: 1.1rem;
            opacity: 0.85;
        }
        .container {
            max-width: 900px;
            margin: 40px auto;
            background: #fff;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }
        h2 {
            margin-top: 40px;
            font-size: 1.6rem;
            color: #0b1d3a;
        }
        pre {
            background: #1e1e1e;
            padding: 15px;
            border-radius: 8px;
            overflow-x: auto;
            color: #eee;
        }
        .disclaimer {
            background: #fff6d6;
            padding: 15px;
            border-left: 4px solid #e6b400;
            border-radius: 6px;
            margin-top: 20px;
            font-size: 0.95rem;
        }
        footer {
            text-align: center;
            padding: 20px;
            color: #555;
            margin-bottom: 40px;
        }
    </style>
</head>

<body>

<header>
    <h1>D1 Volleyball — CMJ Load & Readiness Analysis</h1>
    <p>Mixed Models • Athlete Load Monitoring </p>
</header>

<div class="container">

    <!-- ========================================= -->
    <!-- WITHIN/BETWEEN LOAD MIXED MODEL -->
    <!-- ========================================= -->
    <h2>1. Mixed-Effects Model with Within- and Between-Athlete Load Effects</h2>
    <p>
        This model separates <strong>within-athlete</strong> training fluctuations from <strong>between-athlete</strong> differences by 
        assigning each athlete a random intercept. This approach is standard in applied sports science when evaluating 
        individualized load responses.
    </p>

<pre><code class="language-python">
# Athlete mean loads
CMJ_2['load7_mean']  = CMJ_2.groupby('athlete_id')['jumps_7d'].transform('mean')
CMJ_2['load21_mean'] = CMJ_2.groupby('athlete_id')['jumps_21d'].transform('mean')

# Within-athlete deviation features
CMJ_2['load7_within']  = CMJ_2['jumps_7d']  - CMJ_2['load7_mean']
CMJ_2['load21_within'] = CMJ_2['jumps_21d'] - CMJ_2['load21_mean']

# Mixed model: RSI_Modified explained by within & between load
model = smf.mixedlm(
    "RSI_Modified ~ load7_within + load21_within + load7_mean + load21_mean",
    data=CMJ_2,
    groups="athlete_id"
).fit()

model.summary()
</code></pre>

    <hr>

    <!-- ========================================= -->
    <!-- 2 — LOAD MODELS WITH RAW VARIABLES + LOAD RATIO -->
    <!-- ========================================= -->
    <h2>2. Modeling Athlete Load Using Raw Counts & Load Ratios</h2>
    <p>
        Here, raw load metrics (7d and 21d jump counts) are modeled directly, followed by a feature engineered 
        <strong>load ratio</strong> to detect acute spikes in training.
    </p>

<pre><code class="language-python">
# Mixed model using raw jump load features
model_raw = smf.mixedlm(
    "RSI_Modified ~ jumps_7d + jumps_21d",
    data=CMJ_2,
    groups=CMJ_2['athlete_id']
).fit()

model_raw.summary()

# Load ratio feature
CMJ_2['load_ratio'] = CMJ_2['jumps_7d'] / CMJ_2['jumps_21d']

model_ratio = smf.mixedlm(
    "RSI_Modified ~ load_ratio",
    data=CMJ_2,
    groups=CMJ_2['athlete_id']
).fit()

model_ratio.summary()
</code></pre>

    <hr>

    <!-- ========================================= -->
    <!-- 3 — ATHLETE-SPECIFIC LOAD RESPONSE PLOTS -->
    <!-- ========================================= -->
    <h2>3. Athlete-Specific Regression Visualization</h2>
    <p>
        Each athlete displays a unique load-response curve. Plotting individual regressions 
        emphasizes why random-effects models are necessary in performance tracking research.
    </p>

<pre><code class="language-python">
for athlete, df_a in CMJ_2.groupby('athlete_id'):
    sns.regplot(
        x=df_a['jumps_7d'],
        y=df_a['jump_height'],
        scatter_kws={'alpha':0.6},
        line_kws={'linewidth':1}
    )

plt.title("Jump Height vs 7-Day Load")
plt.xlabel("7-Day Jump Load")
plt.ylabel("Jump Height (cm)")
plt.show()
</code></pre>

    <hr>

    <!-- ========================================= -->
    <!-- 4 — PRE/POST SESSION LOAD CORRECTION -->
    <!-- ========================================= -->
    <h2>4. Adjusting Load Metrics for Pre/Post Session CMJ Tests</h2>
    <p>
        CMJs taken <em>before</em> practice should not include that day's training load. These adjusted metrics 
        correct for that artifact.
    </p>

<pre><code class="language-python">
adjust_map = {
    'active_7d': 'daily_active',
    'energy_7d': 'daily_energy',
    'jumps_7d': 'daily_jumps',
    'high_jumps_7d': 'daily_high_jumps'
}

for load_var, daily_var in adjust_map.items():
    adj = load_var + "_adj"
    CMJ_2[adj] = CMJ_2[load_var]
    CMJ_2.loc[CMJ_2['session_timing'] == "Pre", adj] = \
        CMJ_2[load_var] - CMJ_2[daily_var]
</code></pre>

    <hr>

    <!-- ========================================= -->
    <!-- 5 — FULL LOAD FEATURE MODEL TESTING -->
    <!-- ========================================= -->
    <h2>5. Automated Evaluation Across All Load Features</h2>
    <p>
        Every major load metric is automatically tested to identify which best predicts neuromuscular readiness.
    </p>

<pre><code class="language-python">
features = [
    'active_7d', 'energy_7d', 'jumps_7d', 'high_jumps_7d',
    'active_21d', 'energy_21d', 'jumps_21d', 'high_jumps_21d',
    'acwr_active', 'acwr_energy', 'acwr_jumps'
]

for feature in features:
    df_test = CMJ_2.dropna(subset=[feature, "RSI_Modified", "athlete_id"])

    if df_test.shape[0] > 30:
        model = smf.mixedlm(
            f"RSI_Modified ~ {feature}",
            data=df_test,
            groups=df_test['athlete_id']
        ).fit()

        print("\n========================")
        print(f"RESULTS FOR: {feature}")
        print(model.summary())
</code></pre>

</div>

<footer>
    © 2025 | Data Science Portfolio — Kailani Wang
</footer>

<script>hljs.highlightAll();</script>

</body>
</html>
