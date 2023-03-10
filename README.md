# polars-in-R-solutions

## Easy: Install r-polars directly from precompiled binary, see github Readme.md . Write a lazy query which lazily reads two csv files + join them + filter + column manipulation via expressions. Build the query with at least 15 of the already translated expression functions. Use apply and/or map to execute an R user function within a lazy polars query.


```R
> 
> library(rpolars)
> 
> Data_Frame1 = data.frame(
+     Training = c("Strength", "Stamina", "Other"),
+     Pulse = c(100, 150, 120),
+     Duration = c(60, 30, 45)
+ )
> 
> Data_Frame2 = data.frame(
+     Training = c("Stamina", "Stamina", "Strength"),
+     Pulse = c(140, 150, 160),
+     Duration = c(30, 30, 20)
+ )
> 
> my_file_1 = tempfile()
> write.csv(Data_Frame1, my_file_1)
> LazyFrame_1 = pl$lazy_csv_reader(path=my_file_1)
> 
> my_file_2 = tempfile()
> write.csv(Data_Frame2, my_file_2)
> LazyFrame_2 = pl$lazy_csv_reader(path=my_file_2)
> 
> 
> join_frame = LazyFrame_1$join(LazyFrame_2, on = "Training")
> filter_expr = pl$col("Pulse") > 140
> join_frame$filter(filter_expr)$collect()
polars DataFrame: shape: (2, 7)
┌─────┬──────────┬───────┬──────────┬────────┬─────────────┬────────────────┐
│     ┆ Training ┆ Pulse ┆ Duration ┆ _right ┆ Pulse_right ┆ Duration_right │
│ --- ┆ ---      ┆ ---   ┆ ---      ┆ ---    ┆ ---         ┆ ---            │
│ i64 ┆ str      ┆ i64   ┆ i64      ┆ i64    ┆ i64         ┆ i64            │
╞═════╪══════════╪═══════╪══════════╪════════╪═════════════╪════════════════╡
│ 2   ┆ Stamina  ┆ 150   ┆ 30       ┆ 1      ┆ 140         ┆ 30             │
│ 2   ┆ Stamina  ┆ 150   ┆ 30       ┆ 2      ┆ 150         ┆ 30             │
└─────┴──────────┴───────┴──────────┴────────┴─────────────┴────────────────┘
> 
> join_frame$with_columns(
+     pl$col("Pulse")$abs()$alias("Pulse_right"),
+     (pl$col("Duration")+2)$alias("Duration_right")
+ )$collect()
polars DataFrame: shape: (3, 7)
┌─────┬──────────┬───────┬──────────┬────────┬─────────────┬────────────────┐
│     ┆ Training ┆ Pulse ┆ Duration ┆ _right ┆ Pulse_right ┆ Duration_right │
│ --- ┆ ---      ┆ ---   ┆ ---      ┆ ---    ┆ ---         ┆ ---            │
│ i64 ┆ str      ┆ i64   ┆ i64      ┆ i64    ┆ i64         ┆ f64            │
╞═════╪══════════╪═══════╪══════════╪════════╪═════════════╪════════════════╡
│ 2   ┆ Stamina  ┆ 150   ┆ 30       ┆ 1      ┆ 150         ┆ 32.0           │
│ 2   ┆ Stamina  ┆ 150   ┆ 30       ┆ 2      ┆ 150         ┆ 32.0           │
│ 1   ┆ Strength ┆ 100   ┆ 60       ┆ 3      ┆ 100         ┆ 62.0           │
└─────┴──────────┴───────┴──────────┴────────┴─────────────┴────────────────┘
> 
> join_frame$select(pl$col("Pulse")$mode())$collect()
polars DataFrame: shape: (1, 1)
┌───────┐
│ Pulse │
│ ---   │
│ i64   │
╞═══════╡
│ 150   │
└───────┘
> 
> join_frame$select(pl$col("Pulse")$mean())$collect()
polars DataFrame: shape: (1, 1)
┌────────────┐
│ Pulse      │
│ ---        │
│ f64        │
╞════════════╡
│ 133.333333 │
└────────────┘
> 
> join_frame$select(pl$col("Pulse")$median())$collect()
polars DataFrame: shape: (1, 1)
┌───────┐
│ Pulse │
│ ---   │
│ f64   │
╞═══════╡
│ 150.0 │
└───────┘
> 
> join_frame$select(pl$col("Duration")$sum())$collect()
polars DataFrame: shape: (1, 1)
┌──────────┐
│ Duration │
│ ---      │
│ i64      │
╞══════════╡
│ 120      │
└──────────┘
> 
> join_frame$select(pl$col("Duration")$min())$collect()
polars DataFrame: shape: (1, 1)
┌──────────┐
│ Duration │
│ ---      │
│ i64      │
╞══════════╡
│ 30       │
└──────────┘
> 
> join_frame$select(pl$col("Duration")$rank())$collect()
polars DataFrame: shape: (3, 1)
┌──────────┐
│ Duration │
│ ---      │
│ f32      │
╞══════════╡
│ 1.5      │
│ 1.5      │
│ 3.0      │
└──────────┘
> 
> join_frame$select(pl$col("Duration_right")$sin())$collect()
polars DataFrame: shape: (3, 1)
┌────────────────┐
│ Duration_right │
│ ---            │
│ f64            │
╞════════════════╡
│ -0.988032      │
│ -0.988032      │
│ 0.912945       │
└────────────────┘
> 
> join_frame$select(pl$col("Duration_right")$slice(-3,3))$collect()
polars DataFrame: shape: (3, 1)
┌────────────────┐
│ Duration_right │
│ ---            │
│ i64            │
╞════════════════╡
│ 30             │
│ 30             │
│ 20             │
└────────────────┘
> 
> 
> join_frame$select(pl$col("Pulse_right")$sqrt())$collect()
polars DataFrame: shape: (3, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ f64         │
╞═════════════╡
│ 11.83216    │
│ 12.247449   │
│ 12.649111   │
└─────────────┘
> 
> 
> join_frame$select(pl$col("Pulse_right")$sort())$collect()
polars DataFrame: shape: (3, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ i64         │
╞═════════════╡
│ 140         │
│ 150         │
│ 160         │
└─────────────┘
> 
> 
> join_frame$select(pl$col("Pulse_right")$unique())$collect()
polars DataFrame: shape: (3, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ i64         │
╞═════════════╡
│ 140         │
│ 150         │
│ 160         │
└─────────────┘
> 
> 
> join_frame$select(pl$col("Pulse_right")$var())$collect()
polars DataFrame: shape: (1, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ f64         │
╞═════════════╡
│ 100.0       │
└─────────────┘
> 
> join_frame$select(pl$col("Pulse_right")$std())$collect()
polars DataFrame: shape: (1, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ f64         │
╞═════════════╡
│ 10.0        │
└─────────────┘
> 
> join_frame$select(pl$col("Pulse_right")$sign())$collect()
polars DataFrame: shape: (3, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ i64         │
╞═════════════╡
│ 1           │
│ 1           │
│ 1           │
└─────────────┘
> 
> join_frame$select(pl$col("Pulse_right")$kurtosis())$collect()
polars DataFrame: shape: (1, 1)
┌─────────────┐
│ Pulse_right │
│ ---         │
│ f64         │
╞═════════════╡
│ -1.5        │
└─────────────┘
> 
> 
> e_all =pl$col(pl$dtypes$Int64)
> e_add10  = e_all$apply(\(x)  {x+10})$suffix("_sum")
> join_frame$select(e_add10)
[1] "polars LazyFrame naive plan: (run ldf$describe_optimized_plan() to see the optimized plan)"
   SELECT [col("").map_list().alias("_sum"), col("Pulse").map_list().alias("Pulse_sum"), col("Duration").map_list().alias("Duration_sum"), col("_right").map_list().alias("_right_sum"), col("Pulse_right").map_list().alias("Pulse_right_sum"), col("Duration_right").map_list().alias("Duration_right_sum")] FROM
    INNER JOIN:
    LEFT PLAN ON: [col("Training")]
      CSV SCAN C:\Users\lilad\AppData\Local\Temp\RtmpCIqA0A\file84827723526
      PROJECT */4 COLUMNS
    RIGHT PLAN ON: [col("Training")]
      CSV SCAN C:\Users\lilad\AppData\Local\Temp\RtmpCIqA0A\file8486415156
      PROJECT */4 COLUMNS
    END INNER JOIN

> 


```



## Medium: Use rustup to install rust nightly. Clone r-polars. Restore renv environment. Build/compile the r-polars package locally. Implement a function Expr_sum_add2 which should behave like Expr_sum but also add 2 to the result. The add 2 implementation should be on the rust side (see Expr::add and eager/lazy cookbooks in polars rust api docs). Add documentation and show the function can be used in the package.

Solution : https://github.com/xendai66/r-polars/commit/e1e57515e1c5e29b6e12ffe702d771082a0390d9

Also, currently working on solving hard and easy task as well thanks!
