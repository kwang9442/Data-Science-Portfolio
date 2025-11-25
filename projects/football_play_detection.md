# D1 Football — Play Detection Project

### Overview
This project detects football plays from player-tracking data using:
- abrupt movement change detection  
- interval tree period matching  
- change-point detection (PELT algorithm)  
- K-Means clustering of trajectories  

---

## 1. Abrupt Movement Detection (Python)

```python
# Abrupt velocity + position changes
def abrupt_changes(DataF, percent_change, max_time_interval):
    abs_change_vel = abs(DataF['v'].diff())
    abs_change_x = abs(DataF['x'].diff())
    abs_change_y = abs(DataF['y'].diff())
    
    change_thresh_v = (DataF['v'].max() - DataF['v'].min()) * percent_change
    change_thresh_x = (DataF['x'].max() - DataF['x'].min()) * percent_change
    change_thresh_y = (DataF['y'].max() - DataF['y'].min()) * percent_change
    
    abrupt = pd.Series(False, index=DataF.index)
    
    for i in range(1, len(DataF)):
        if (
            abs_change_vel.iloc[i] >= change_thresh_v or
            abs_change_x.iloc[i] >= change_thresh_x or
            abs_change_y.iloc[i] >= change_thresh_y
        ):
            abrupt.iloc[i] = True
            for j in range(1, max_time_interval + 1):
                if i + j < len(DataF):
                    abrupt.iloc[i + j] = False

    return abrupt
---
## 2. IntervalTree Matching of Practice Periods

from intervaltree import IntervalTree

trees = {}

for athlete_id, group in periods.groupby('athlete_id'):
    tree = IntervalTree()
    for idx, row in group.iterrows():
        start = row['start_time']
        end = row['end_time']
        if start != end:
            tree.addi(start, end, row['period_name'])
    trees[athlete_id] = tree

def lookup_period(row):
    athlete = row['athlete_id']
    time = row['time']
    if athlete in trees:
        matches = trees[athlete][time]
        if matches:
            return list(matches)[0].data
    return pd.NA
---

## 3. Change-Point Detection (PELT)

def detect_change_and_plot(data, label):
    v_series = data['v'].values
    algo = rpt.Pelt(model="rbf").fit(v_series)
    change_points = algo.predict(pen=30)

    plt.figure(figsize=(12, 4))
    plt.plot(data['time'], v_series, label=f'{label}')
    for cp in change_points[:-1]:
        plt.axvline(x=data['time'].iloc[cp], color='r', linestyle='--')
    plt.title(f'Velocity changes detected during {label}')
    plt.xlabel('Time')
    plt.ylabel('Velocity')
    plt.legend()
    plt.grid(True)
    plt.show()

---

## 4. Clustering Player Trajectories

from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

player_df = df_plot_filtered.copy()

trajectories = []
for start, end, _ in segments:
    traj = player_df[(player_df['time'] >= start) & (player_df['time'] <= end)][['x', 'y']]
    if len(traj) >= 5:
        traj_uniform = traj.reset_index(drop=True).interpolate().iloc[:35].values.flatten()
        trajectories.append(traj_uniform)

X = np.vstack(trajectories)
X = StandardScaler().fit_transform(X)

kmeans = KMeans(n_clusters=3, random_state=0)
labels = kmeans.fit_predict(X)

for i, traj in enumerate(trajectories):
    traj_reshaped = traj.reshape(-1, 2)
    plt.plot(traj_reshaped[:, 0], traj_reshaped[:, 1], label=f"Cluster {labels[i]}", alpha=0.6)


