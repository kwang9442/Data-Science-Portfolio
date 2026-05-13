<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D1 Volleyball — CMJ Load & Readiness Analysis</title>

    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <!-- Highlight.js -->
    <link rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/default.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>

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

        header p {
            margin-top: 12px;
            font-size: 1.05rem;
            opacity: 0.9;
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
            margin-top: 42px;
            color: #0b1d3a;
            font-size: 1.7rem;
        }

        p {
            line-height: 1.7;
        }

        ul {
            line-height: 1.7;
        }

        .pipeline {
            background: #f1f5f9;
            padding: 15px;
            border-left: 4px solid #0b1d3a;
            border-radius: 6px;
            margin-top: 10px;
            line-height: 1.6;
        }

        pre {
            background: #1e1e1e;
            color: #eee;
            padding: 14px;
            border-radius: 8px;
            overflow-x: auto;
            margin-top: 20px;
        }

        pre code {
            color: #7aa2f7;
            font-size: 0.93rem;
        }

        .note {
            background: #fff3cd;
            padding: 14px;
            border-left: 4px solid #e0b400;
            border-radius: 6px;
            margin-top: 20px;
            font-size: 0.95rem;
            line-height: 1.6;
        }

        .section-image {
            text-align: center;
            margin-top: 25px;
        }

        .section-image img {
            max-width: 100%;
            border-radius: 10px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }

        .double-image {
            display: flex;
            justify-content: center;
            gap: 24px;
            margin-top: 24px;
            flex-wrap: wrap;
        }

        .double-image img {
            width: 46%;
            border-radius: 10px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }

        footer {
            text-align: center;
            padding: 25px;
            color: #555;
            margin-bottom: 20px;
        }

    </style>
</head>

<body>

<header>
    <h1>D1 Volleyball — CMJ Load & Readiness Analysis</h1>
    <p>Sports Analytics • Mixed Models • Athlete Monitoring • Load Analysis</p>
</header>

<div class="container">

    <!-- ====================================================== -->
    <!-- PROJECT OVERVIEW -->
    <!-- ====================================================== -->

    <h2>Project Overview</h2>

    <p>
        This project evaluated whether Countermovement Jump (CMJ) metrics can be used to monitor
        athlete fatigue, workload adaptation, and neuromuscular readiness in NCAA Division I volleyball athletes.
    </p>

    <p>
        Using force plate testing data alongside wearable jump-load monitoring data,
        I built a full data analysis workflow involving data cleaning, feature engineering,
        longitudinal athlete monitoring, mixed-effects modeling, and statistical hypothesis testing.
    </p>

    <p>
        The primary goal was to determine whether short-term and long-term workload metrics
        meaningfully predict changes in CMJ performance variables such as RSI_Modified,
        Jump Height, Peak Takeoff Power, and Contraction Time.
    </p>

    <!-- ====================================================== -->
    <!-- PIPELINE -->
    <!-- ====================================================== -->

    <h2>End-to-End Analytics Pipeline</h2>

    <div class="pipeline">
        Raw CMJ Force Plate Data + VERT IMU Load Data →
        Data Cleaning & Standardization →
        Athlete ID Matching →
        Pre/Post Practice Pairing →
        Feature Engineering →
        Acute Load Statistical Testing →
        Rolling Load Calculations →
        Mixed Effects Modeling →
        Athlete-Specific Trend Analysis →
        Interpretation of Fatigue & Adaptation Responses
    </div>

    <!-- ====================================================== -->
    <!-- DATA SOURCES -->
    <!-- ====================================================== -->

    <h2>1. Data Sources & Performance Metrics</h2>

    <p>
        Two primary datasets were combined for this project:
    </p>

    <ul>
        <li>
            <strong>VALD ForceDecks CMJ Data</strong> — force plate measurements from
            pre- and post-practice Countermovement Jump tests.
        </li>

        <li>
            <strong>VERT IMU Load Data</strong> — wearable athlete monitoring data
            containing jump counts, movement load estimates, and rolling workload metrics.
        </li>
    </ul>

    <p>
        Relevant CMJ performance metrics included:
    </p>

    <ul>
        <li>Jump Height</li>
        <li>RSI_Modified</li>
        <li>Peak Takeoff Force</li>
        <li>Peak Takeoff Power</li>
        <li>Takeoff Velocity</li>
        <li>Contraction Time</li>
        <li>Countermovement Depth</li>
    </ul>

    <p>
        Relevant workload metrics included:
    </p>

    <ul>
        <li>7-Day and 21-Day Jump Counts</li>
        <li>Energy Load</li>
        <li>Active Minutes</li>
        <li>High Intensity Jump Counts (15+ / 20+ inch jumps)</li>
        <li>Acute:Chronic Workload Ratios (ACWR)</li>
    </ul>

    <!-- IMAGE PLACEHOLDER -->
    <div class="double-image">
        <img src="../images/vald-placeholder.png" alt="VALD ForceDecks">
        <img src="../images/vert-placeholder.png" alt="VERT IMU">
    </div>

    <!-- ====================================================== -->
    <!-- DATA CLEANING -->
    <!-- ====================================================== -->

    <h2>2. Data Cleaning & Dataset Construction</h2>

    <p>
        The raw datasets required extensive preprocessing before statistical analysis could begin.
        Athlete IDs were inconsistent between systems, timestamps used different formats,
        and several force plate variables contained malformed string formatting that had to be cleaned manually.
    </p>

    <p>
        Key preprocessing steps included:
    </p>

    <ul>
        <li>Standardized timestamps and converted UTC timezones</li>
        <li>Matched athlete IDs across multiple datasets</li>
        <li>Removed incomplete or missing jump records</li>
        <li>Paired pre-practice and post-practice CMJ sessions</li>
        <li>Converted malformed numeric columns into usable float values</li>
        <li>Created athlete-day level merged datasets</li>
    </ul>

<pre><code class="language-python">
CMJ_use['recordedUTC'] = pd.to_datetime(
    CMJ['recordedUTC'],
    utc=True,
    errors='coerce'
)

CMJ_use['time'] = CMJ_use['recordedUTC'].dt.tz_convert('US/Pacific')
CMJ_use['date'] = CMJ_use['time'].dt.date

vert_use['start_date'] = pd.to_datetime(
    vert_use['start_date'],
    format='%d/%m/%Y',
    errors='coerce'
)

vert_use['date'] = vert_use['start_date'].dt.date
</code></pre>

    <p>
        After cleaning and merging, the final dataset contained:
    </p>

    <ul>
        <li>10 NCAA Division I volleyball athletes</li>
        <li>1,156 total CMJ tests</li>
        <li>40 monitored practices</li>
        <li>Rolling workload metrics across the season</li>
    </ul>

    <!-- ====================================================== -->
    <!-- PRE POST -->
    <!-- ====================================================== -->

    <h2>3. Acute Fatigue Analysis — Pre vs Post Practice CMJs</h2>

    <p>
        To evaluate immediate fatigue responses, pre-practice and post-practice CMJ tests
        were paired within each athlete and practice date.
    </p>

    <p>
        For each athlete-day pair, post-practice values were subtracted from pre-practice values
        to measure acute changes in performance after training.
    </p>

<pre><code class="language-python">
for col in numeric_cols:
    CMJ_wide[f'{col}_diff'] = (
        CMJ_wide[f'{col}_Post']
        - CMJ_wide[f'{col}_Pre']
    )
</code></pre>

    <p>
        Each metric was standardized and visualized using boxplots
        to compare distributions of post-pre changes across athletes.
    </p>

<pre><code class="language-python">
sns.boxplot(
    data=melted,
    x='Metric',
    y='Difference',
    color='skyblue'
)

plt.axhline(0, color='black', linewidth=1)
</code></pre>

    <p>
        One-sample t-tests were then used to determine whether the average post-practice
        changes differed significantly from zero.
    </p>

<pre><code class="language-python">
tstat, pval = ttest_1samp(subset, 0)
</code></pre>

    <p>
        Most CMJ metrics showed very small acute changes following practice,
        suggesting minimal immediate fatigue effects.
    </p>

    <p>
        However, Peak Takeoff Power and Countermovement Depth showed statistically significant
        increases post-practice, which was somewhat unexpected since higher training load is often
        associated with reduced explosive performance.
    </p>

    <div class="note">
        One interpretation is that athletes were highly adapted to their training load,
        or that post-practice tests reflected improved warm-up and neuromuscular activation
        rather than acute fatigue.
    </div>

    <!-- IMAGE PLACEHOLDER -->
    <div class="section-image">
        <img src="../images/pre-post-boxplot.png" alt="Pre Post CMJ Boxplots">
    </div>

    <!-- ====================================================== -->
    <!-- PAIRED T TEST -->
    <!-- ====================================================== -->

    <h2>4. Paired Statistical Testing</h2>

    <p>
        Paired t-tests were also performed directly on pre-practice and post-practice CMJ values
        to quantify whether practices significantly altered athlete performance metrics.
    </p>

<pre><code class="language-python">
for metric in metrics:

    pre = CMJ_wide[f'{metric}_Pre']
    post = CMJ_wide[f'{metric}_Post']

    t_stat, p_val = ttest_rel(post, pre)

    print(f"{metric}: t={t_stat:.2f}, p={p_val:.4f}")
</code></pre>

    <p>
        This analysis confirmed that most performance metrics remained relatively stable
        across practices, with only a few variables showing meaningful shifts.
    </p>

    <p>
        These results suggest that single-practice fatigue effects may be less important
        than longer-term workload accumulation in this athlete population.
    </p>

    <!-- ====================================================== -->
    <!-- REGRESSION -->
    <!-- ====================================================== -->

    <h2>5. Regression Modeling of Jump Performance</h2>

    <p>
        Multiple linear regression models were created to evaluate whether pre-practice CMJ metrics
        could predict post-practice jump performance.
    </p>

<pre><code class="language-python">
features = CMJ_wide[[
    'JUMP_HEIGHT_Pre',
    'PEAK_TAKEOFF_POWER_Pre',
    'TAKEOFF_VELOCITY_Pre',
    'COUNTERMOVEMENT_DEPTH_Pre'
]]

target = CMJ_wide['JUMP_HEIGHT_Post']

multi_model = LinearRegression()
multi_model.fit(features, target)
</code></pre>

    <p>
        Pre-practice Jump Height and Takeoff Velocity showed strong predictive relationships
        with post-practice jump performance.
    </p>

    <p>
        In contrast, Countermovement Depth and Peak Takeoff Power had weaker independent effects
        once multiple variables were included together in the model.
    </p>

    <!-- ====================================================== -->
    <!-- ROLLING LOAD -->
    <!-- ====================================================== -->

    <h2>6. Rolling Workload Calculations</h2>

    <p>
        To evaluate chronic training stress, rolling workload metrics were constructed
        using cumulative jump counts across 7-day and 21-day windows.
    </p>

    <p>
        These rolling features were designed to capture both short-term and long-term
        workload accumulation throughout the season.
    </p>

<pre><code class="language-python">
all_jumps = np.cumsum(jumps, axis=1)

jumps21 = (
    all_jumps[:,21:]
    - all_jumps[:,:-21]
)
</code></pre>

    <p>
        Acute:Chronic Workload Ratios (ACWR) were also examined to identify
        potential workload spikes relative to longer-term athlete conditioning.
    </p>

    <!-- IMAGE PLACEHOLDER -->
    <div class="section-image">
        <img src="../images/rolling-loads.png" alt="Rolling Jump Load">
    </div>

    <!-- ====================================================== -->
    <!-- MIXED MODELS -->
    <!-- ====================================================== -->

    <h2>7. Mixed Effects Modeling</h2>

    <p>
        Athlete monitoring data contains repeated measurements from the same athletes over time,
        making mixed-effects modeling appropriate for handling correlated observations.
    </p>

    <p>
        Random intercept mixed models were used to separate:
    </p>

    <ul>
        <li>Between-athlete baseline differences</li>
        <li>Within-athlete workload fluctuations</li>
    </ul>

<pre><code class="language-python">
CMJ_2['mean_7D'] = (
    CMJ_2.groupby('profileID')['7D Jumps']
    .transform('mean')
)

CMJ_2['within_7D'] = (
    CMJ_2['7D Jumps']
    - CMJ_2['mean_7D']
)

results = smf.mixedlm(
    'RSI_MODIFIED ~ within_7D + mean_7D',
    data=CMJ_2,
    groups='profileID'
).fit()
</code></pre>

    <p>
        This framework allowed the analysis to answer:
    </p>

    <ul>
        <li>
            Does an athlete's RSI change when their weekly jump load increases relative to their own baseline?
        </li>

        <li>
            Are athletes with consistently higher workloads fundamentally different from lower-load athletes?
        </li>
    </ul>

    <!-- ====================================================== -->
    <!-- ATHLETE PLOTS -->
    <!-- ====================================================== -->

    <h2>8. Athlete-Specific Load Response Visualization</h2>

    <p>
        Athlete-specific regression visualizations were created to examine
        how workload relationships differed across individual players.
    </p>

<pre><code class="language-python">
for pid, group in CMJ_2.groupby('profileID'):

    sns.regplot(
        x=group['7D Jumps'],
        y=group['JUMP_HEIGHT'],
        scatter=True,
        scatter_kws={'alpha':0.6},
        line_kws={'linewidth':1}
    )
</code></pre>

    <p>
        The resulting trends showed substantial variability between athletes,
        reinforcing the importance of individualized monitoring approaches
        rather than relying entirely on team-wide averages.
    </p>

    <p>
        Most athletes showed relatively weak relationships between jump load
        and CMJ performance, suggesting that chronic workload did not strongly
        impair neuromuscular readiness in this dataset.
    </p>

    <!-- IMAGE PLACEHOLDER -->
    <div class="section-image">
        <img src="../images/mixed-model-athletes.png" alt="Mixed Model Athlete Lines">
    </div>

    <!-- ====================================================== -->
    <!-- MULTICOLLINEARITY -->
    <!-- ====================================================== -->

    <h2>9. Multicollinearity & Feature Evaluation</h2>

    <p>
        Many workload metrics were highly correlated with one another,
        creating multicollinearity concerns within regression models.
    </p>

    <p>
        Variance Inflation Factor (VIF) testing and correlation matrices were used
        to evaluate redundancy between workload variables.
    </p>

<pre><code class="language-python">
for i in range(fit_data[load_vars].shape[1]):

    print(
        load_vars[i],
        variance_inflation_factor(
            fit_data[load_vars],
            i
        )
    )
</code></pre>

    <p>
        Several models were simplified into reduced variable sets
        to improve interpretability and statistical stability.
    </p>

    <!-- ====================================================== -->
    <!-- FULL MODEL -->
    <!-- ====================================================== -->

    <h2>10. Full Mixed Model Evaluation Across Performance Metrics</h2>

    <p>
        Finally, mixed models were applied across multiple CMJ outcome variables
        to evaluate how workload influenced different dimensions of athletic performance.
    </p>

<pre><code class="language-python">
for target in [

    'JUMP_HEIGHT',
    'RSI_MODIFIED',
    'PEAK_TAKEOFF_POWER',
    'TAKEOFF_VELOCITY',
    'CONTRACTION_TIME',
    'COUNTERMOVEMENT_DEPTH'

]:

    model = smf.mixedlm(
        model_str,
        data=fit_data,
        groups='profileID'
    ).fit()

    pred = model.predict(fit_data)

    plt.scatter(fit_data[target], pred)
    plt.show()
</code></pre>

    <p>
        Most workload variables showed weak or slightly positive relationships
        with CMJ performance metrics.
    </p>

    <p>
        This was somewhat counterintuitive since higher workload is often expected
        to reduce neuromuscular readiness through fatigue accumulation.
    </p>

    <p>
        One possible interpretation is that athletes adapted effectively
        to increasing seasonal workloads and improved throughout the competitive season.
    </p>

    <!-- ====================================================== -->
    <!-- WHAT IT SHOWS -->
    <!-- ====================================================== -->

    <h2>What This Project Demonstrates</h2>

    <ul>
        <li>Built a full sports analytics workflow using real athlete monitoring data.</li>

        <li>Cleaned and merged multi-source longitudinal datasets.</li>

        <li>Applied mixed-effects statistical modeling for repeated athlete measurements.</li>

        <li>Engineered rolling workload and ACWR features from wearable sensor data.</li>

        <li>Performed paired statistical testing and regression analysis.</li>

        <li>Visualized individualized athlete load-response relationships.</li>

        <li>Investigated multicollinearity and model interpretability issues.</li>

        <li>Translated statistical results into applied sports science interpretations.</li>
    </ul>

    <!-- ====================================================== -->
    <!-- ONGOING -->
    <!-- ====================================================== -->

    <h2>Ongoing Work</h2>

    <p>
        Current work includes improving athlete-specific longitudinal modeling,
        incorporating nonlinear workload relationships,
        and exploring whether seasonal trends or practice intensity classifications
        better explain CMJ readiness changes over time.
    </p>

    <p>
        Future extensions may also include:
    </p>

    <ul>
        <li>Random slope mixed models</li>
        <li>Time-series forecasting</li>
        <li>Fatigue state clustering</li>
        <li>Practice classification modeling</li>
        <li>Athlete-specific adaptation curves</li>
    </ul>

</div>

<footer>
    © 2025 | Data Science Portfolio — Kailani Wang
</footer>

<script>
    hljs.highlightAll();
</script>

</body>
</html>
