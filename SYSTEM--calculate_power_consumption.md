# Calculate approximate power consumption

> ### Example
>
> [*Lenovo ThinkCentre M92p Tiny*](https://github.com/scubamuc/scubamuc.github.io#11-hardware) headless power consumption: ~16W load / ~12W idle
> 
> You'll need to run `sysstat` on your machine for 24 Hours to get an average with `sar -p`
>
> ![grafik](https://github.com/user-attachments/assets/beedb833-ba69-4e7a-8ad9-0a2770184cda)
> 
> + Average idle load: 91,04%
> + Average CPU load: 8,96%
> + Averege CPU load: 8,96% / 4CPU = 2,24%

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

