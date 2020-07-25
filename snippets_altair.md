# Snippets for ALtair (Vega-Lite high level implementation)

## Bar Chart

``` python
  alt.Chart(df).mark_bar().encode(
      x='df.v1',
      y='mean(df.v2)',
      color='df.v1'
  ).properties(
      title="XXXX",
      width=400
  )
```

## Histogram

``` python
  alt.Chart(df).mark_bar().encode(
      x=alt.X('df.v1',bin=alt.Bin(maxbins=50)),
      y="count()"
      color='df.v1'
  ).properties(
      title="XXXX",
      width=400
  )
```

## Line Chart

``` python
  alt.Chart(df).mark_line().encode(
    x = 'yearmonth(df.v1):T',
    y='mean(df.v2)',
    color='df.v3'
  ).properties(
    title="XXXX",
    width=400
  )
```

## Scatter Plot

```python
  alt.Chart(df).mark_point().encode(
    x='df.v1',
    y='df.v2'
    )
```

## Composition (multigraph)

```python
  ((graph1|graph2)&graph3).properties(
    title="XXXX"
  )

```

## Map

```python
  countries = alt.topo_feature("https://cdn.jsdelivr.net/npm/world-atlas@2/countries-110m.json",
                             feature="countries")

  background = alt.Chart(countries).mark_geoshape(
      fill="lightgrey",
      stroke="white"
  )

  temp = weather_df.groupby("city").mean().reset_index()
  cities = alt.Chart(temp).mark_point().encode(
      latitude="lat",
      longitude="lon",
      size=alt.value(1),
      color=alt.Color("temp",scale=alt.Scale(range=["red","orange","lightblue"]))
  )

  background+cities
```

## Interactivity (single selection)

```python
  select_ctry = alt.selection(type="single",encodings=["x"])

  temp = weather_df.groupby(["city","country"]).mean().reset_index()

  ranking_city = alt.Chart(temp).mark_bar().encode(
      x=alt.X("city",
              sort=alt.Sort(field="temp",
                            op="mean",
                            order="descending"),

              ),
      y="mean(temp)",
      tooltip="city"
  ).properties(
      width=800,
      title="Ranking de ciudades por temperatura media"
  ).transform_filter(
      select_ctry
  )

  ranking_country = alt.Chart(temp).mark_bar().encode(
      x=alt.X("country",
              sort=alt.Sort(field="temp",
                            op="mean",
                            order="descending"),
              axis=None
              ),
      y="mean(temp)",
      tooltip="country"
  ).properties(
      width=800,
      title="Ranking de ciudades por temperatura media",
      selection=select_ctry
  )

  ranking_country&ranking_city

```
## Interactivity (interval selection)

```python
   select_st = alt.selection(type="interval",encodings=["x"])

  comp_trends = alt.Chart(trends).mark_bar().encode(
      x='search_term',
      y='mean(value)',
      color=alt.condition(select_st, 'search_term', alt.value('lightgray')),
      tooltip="search_term"
  ).properties(
      width=100,
      selection=select_st
  )

  trends_line = alt.Chart(trends).mark_line().encode(
      x="yearmonth(date):T",
      y='mean(value)',
      color="search_term"
  ).properties(
      width=600
  ).transform_filter(
      select_st
  )

  (comp_trends|trends_line).properties(
      title="Búsquedas de Google de términos de data science"
  ) 
  
```

## Trail

```python
troops_viz = alt.Chart(troops).mark_trail(
    strokeCap="square"
).encode(
    latitude="lat",
    longitude="lon",
    color=alt.Color("direction",scale=alt.Scale(domain=['A', 'R'],range=["#EBD2A8","#888888"])),
    size=alt.Size("survivors",scale=alt.Scale(range=[1,75])),
    detail="division"
).properties(
    width=800
)
```
