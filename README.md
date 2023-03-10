# polars-in-R-solutions

## Easy: Install r-polars directly from precompiled binary, see github Readme.md . Write a lazy query which lazily reads two csv files + join them + filter + column manipulation via expressions. Build the query with at least 15 of the already translated expression functions. Use apply and/or map to execute an R user function within a lazy polars query.


```R 
library(rpolars)

Data_Frame1 = data.frame(
  Training = c("Strength", "Stamina", "Other"),
  Pulse = c(100, 150, 120),
  Duration = c(60, 30, 45)
)

Data_Frame2 = data.frame(
  Training = c("Stamina", "Stamina", "Strength"),
  Pulse = c(140, 150, 160),
  Duration = c(30, 30, 20)
)

my_file_1 = tempfile()
write.csv(Data_Frame1, my_file_1)
LazyFrame_1 = pl$lazy_csv_reader(path=my_file_1)

my_file_2 = tempfile()
write.csv(Data_Frame2, my_file_2)
LazyFrame_2 = pl$lazy_csv_reader(path=my_file_2)


join_frame = LazyFrame_1$join(LazyFrame_2, on = "Training")
filter_expr = pl$col("Pulse") > 140
join_frame$filter(filter_expr)$collect()

join_frame$with_columns(
     pl$col("Pulse")$abs()$alias("Pulse_right"),
     (pl$col("Duration")+2)$alias("Duration_right")
)$collect()

join_frame$select(pl$col("Pulse")$mode())$collect()

join_frame$select(pl$col("Pulse")$mean())$collect()

join_frame$select(pl$col("Pulse")$median())$collect()

join_frame$select(pl$col("Duration")$sum())$collect()

join_frame$select(pl$col("Duration")$min())$collect()

join_frame$select(pl$col("Duration")$rank())$collect()

join_frame$select(pl$col("Duration_right")$sin())$collect()

join_frame$select(pl$col("Duration_right")$slice(-3,3))$collect()


join_frame$select(pl$col("Pulse_right")$sqrt())$collect()


join_frame$select(pl$col("Pulse_right")$sort())$collect()


join_frame$select(pl$col("Pulse_right")$unique())$collect()


join_frame$select(pl$col("Pulse_right")$var())$collect()

join_frame$select(pl$col("Pulse_right")$std())$collect()

join_frame$select(pl$col("Pulse_right")$sign())$collect()

join_frame$select(pl$col("Pulse_right")$kurtosis())$collect()


 e_all =pl$col(pl$dtypes$Int64)
 e_add10  = e_all$apply(\(x)  {x+10})$suffix("_sum")
 join_frame$select(e_add10)


```



## Medium: Use rustup to install rust nightly. Clone r-polars. Restore renv environment. Build/compile the r-polars package locally. Implement a function Expr_sum_add2 which should behave like Expr_sum but also add 2 to the result. The add 2 implementation should be on the rust side (see Expr::add and eager/lazy cookbooks in polars rust api docs). Add documentation and show the function can be used in the package.

Solution : https://github.com/xendai66/r-polars/commit/e1e57515e1c5e29b6e12ffe702d771082a0390d9

Also, currently working on solving hard and easy task as well thanks!
