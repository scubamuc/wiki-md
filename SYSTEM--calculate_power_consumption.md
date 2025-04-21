# Calculate approximate power consumption

> ### Example
>
> [*Lenovo ThinkCentre M92p Tiny*](https://github.com/scubamuc/scubamuc.github.io#11-hardware) headless power consumption: ~16W load / ~12W idle
> 
> You'll need to run `sysstat` on your machine for 24 Hours to get an average with `sar -p`
>
> ![grafik](https://github.com/user-attachments/assets/d34f9678-b4b1-4f35-ab25-a20d55476f8e)

> 
> + Example Average idle load: 96,87%
> + Example Average CPU load: 3,13%
> + Example Averege CPU load per CPU: 3,13% / 4CPU = 0,78%

```
W = Watt
H = Hour
D = Day
KW = Kilowatt (/1000)
Cost per KW = €/$ 0,xx (depending on service provider/region)
p.a. = per annum (year)
p.m. = per month
```
## Calculation:

 - Example 1: `12W x 24H x 365D / 1000 x €0,45` = **€ 47,30 p.a.** ~ **€ 3,94 p.m.** 

 - Example 2: `12W x 24H x 365D / 1000 x $0,70` = **$ 73,58 p.a.** ~ **$ 6,13 p.m.** 

