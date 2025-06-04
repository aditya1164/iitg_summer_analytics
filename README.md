# iitg_summer_analytics
#assignment 1




import pandas as pd

df=pd.read_csv(r"C:\Users\Aditya\Desktop\Cars.csv")

df.shape

df.columns

df.set_index('name', inplace=True)

df.mpg.unique()

df['metrics']=df['horsepower']/df['weight']
df.set_index('metrics', inplace=True)

df['horsepower'].max()

df[df['mpg']>=35]

jap=df[df['origin']=='japan']['acceleration'].mean()
print(round(jap,2))

x= df[(df['horsepower'] > 100) & (df['weight'] < 3000)]
a=x['origin'].mode()[0]
print(a)

meanx= df.groupby('model_year')['mpg'].mean()
maxx = meanx.idxmax()
print(maxx)

medianx = df['mpg'].median()
abo = df[df['mpg'] >medianx].copy()
abo['hp_to_weight'] = abo['horsepower'] / abo['weight']
maxr = abo['hp_to_weight'].max()
best = abo[abo['hp_to_weight'] == maxr]
print(best)


import matplotlib.pyplot as plt
avg_mpg = df.groupby(['model_year', 'origin'])['mpg'].mean().reset_index()
plt.figure(figsize=(10, 6))
for origin in avg_mpg['origin'].unique():
    data = avg_mpg[avg_mpg['origin'] == origin]
    plt.plot(data['model_year'], data['mpg'], marker='o', label=origin)
plt.title('Average MPG Over Years by Origin')
plt.xlabel('Year')
plt.ylabel('Average MPG')
plt.legend(title='Origin')
plt.grid()
plt.show()


import seaborn as sns
import matplotlib.pyplot as plt
df.columns = df.columns.str.strip().str.lower()
df_clean = df.dropna(subset=['horsepower', 'weight', 'mpg', 'origin'])
plt.figure(figsize=(10, 6))
sns.set(style="whitegrid")
sns.scatterplot(
    data=df_clean.reset_index(drop=True),  # avoid duplicate index issue
    x='horsepower',
    y='weight',
    hue='origin',
    size='mpg',
    sizes=(40, 200),
    palette='deep',
    hue_order=['japan', 'europe', 'usa'],
    alpha=0.7,
    edgecolor='black'
)
plt.title('Horsepower vs Weight of Cars (Bubble Size = MPG)', fontsize=14)
plt.xlabel('Horsepower')
plt.ylabel('Weight')
plt.legend(title='Origin', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show()


df.columns = df.columns.str.strip().str.lower()
car_stats = (
    df.groupby('name')
      .agg(
          count=('mpg', 'count'),
          mpg_std=('mpg', 'std'),
          mpg_avg=('mpg', 'mean'),
          years=('model_year', pd.Series.nunique)
      )
      .reset_index()
)
consistent_cars = car_stats[
    (car_stats['years'] > 1) & 
    (car_stats['mpg_std'] < 1.0)
]
result = consistent_cars.sort_values(
    by=['count', 'mpg_avg'],
    ascending=[False, False]
)[['name', 'count', 'mpg_avg']]
print(result)

