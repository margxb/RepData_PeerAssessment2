---
title: "Weather Event Analysis"
output:
  pdf_document: default
  html_document: default
---


```r
library(ggplot2)
library(patchwork)

weather <- read.csv("repdata_data_StormData.csv.bz2")
weather['EVTYPE'] <- toupper(weather$EVTYPE)
weather <- weather[!grepl("SUMMARY", weather$EVTYPE),]
weather$EVTYPE[grepl("LAKE", weather$EVTYPE) & grepl("FLOO", weather$EVTYPE)] = "LAKESHORE FLOOD"
weather$EVTYPE[grepl("COAST|TID|CSTL|COASTAL", weather$EVTYPE) & grepl("FLOO", weather$EVTYPE)] = "COASTAL FLOOD"
weather$EVTYPE[grepl("FLASH", weather$EVTYPE) & grepl("FLOO", weather$EVTYPE)] = "FLASH FLOOD"
weather$EVTYPE[!grepl("COASTAL|LAKESHORE|FLASH", weather$EVTYPE) & grepl("FLOOD|FLD", weather$EVTYPE)] = "FLOOD"
weather$EVTYPE[!grepl("MARINE", weather$EVTYPE) & grepl("TSTM|THUNDERSTORM|THU|TUN", weather$EVTYPE) & grepl("WIN", weather$EVTYPE)] = "THUNDERSTORM WIND"
weather$EVTYPE[!grepl("MARINE|THUNDERSTORM|HIGH", weather$EVTYPE) & grepl("STRONG|STORM|ADVISORY", weather$EVTYPE) & grepl("WIN", weather$EVTYPE)] = "STRONG WIND"
weather$EVTYPE[!grepl("MARINE|THUNDERSTORM|HIGH", weather$EVTYPE) & !grepl("STRONG|STORM|ADVISORY|GUSTY|CHILL|GRADIENT", weather$EVTYPE) & grepl("WIN|WND", weather$EVTYPE)] = "WIND"
weather$EVTYPE[grepl("GUST", weather$EVTYPE) & grepl("WIN", weather$EVTYPE)] = "GUSTY WIND"
weather$EVTYPE[grepl("GRADIENT", weather$EVTYPE) & grepl("WIN", weather$EVTYPE)] = "GRADIENT WIND"
weather$EVTYPE[!grepl("MARINE", weather$EVTYPE) & grepl("HIGH", weather$EVTYPE) & grepl("WIND", weather$EVTYPE)] = "HIGH WIND"
weather$EVTYPE[!grepl("MARINE", weather$EVTYPE) & grepl("STRONG", weather$EVTYPE) & grepl("WIND", weather$EVTYPE)] = "STRONG WIND"
weather$EVTYPE[!grepl("EXTREME", weather$EVTYPE) & grepl("CHILL|COLD", weather$EVTYPE)] = "COLD/WIND CHILL"
weather$EVTYPE[grepl("EXTREME|RECORD|EXCESSIVE|UNUSUALLY|UNSEASONAB|PROLONG|EXTENDED", weather$EVTYPE) & grepl("CHI|COLD", weather$EVTYPE)] = 'EXTREME COLD/WIND CHILL'
weather$EVTYPE[grepl("WATERSPOUT|WATER SPOUT|SPOUT", weather$EVTYPE)] = "WATERSPOUT"
weather$EVTYPE[grepl("DROUGHT|RECORD LOW RAINFALL|DRIEST MONTH|ABNORMALLY DRY|DRYNESS|DRY", weather$EVTYPE)] = "DROUGHT"
weather$EVTYPE[grepl("TORNADO|TORN", weather$EVTYPE)] = "TORNADO"
weather$EVTYPE[grepl("ICE|COLD", weather$EVTYPE) & grepl("FOG", weather$EVTYPE)] = "FREEZING FOG"
weather$EVTYPE[grepl("DENSE", weather$EVTYPE) & grepl("FOG", weather$EVTYPE)] = "DENSE FOG"
weather$EVTYPE[!grepl("MARINE", weather$EVTYPE) & grepl("HAIL", weather$EVTYPE)] = "HAIL"
weather$EVTYPE[grepl("DUST", weather$EVTYPE) & grepl("DEVEL", weather$EVTYPE)] = "DUST DEVIL"
weather$EVTYPE[grepl("DUSTSTORM|BLOWING DUST|SAHARAN DUST", weather$EVTYPE)] = "DUST STORM"
weather$EVTYPE[grepl("WAVE|BURST", weather$EVTYPE) & grepl("HEAT", weather$EVTYPE)] = "HEAT"
weather$EVTYPE[grepl("RECORD|EXTREME|UNSEASON|ABNORMAL|UNUSUAL", weather$EVTYPE) & grepl("HEAT|WARM|HOT|TEMPERATURE|HIGH", weather$EVTYPE)] = "EXCESSIVE HEAT"
weather$EVTYPE[grepl("FROST|FREEZE", weather$EVTYPE)] = "FROST/FREEZE"
weather$EVTYPE[grepl("UNSEASON|RECORD|LOW", weather$EVTYPE) & grepl("COOL|LOW|TEMPERATURE", weather$EVTYPE)] = "COLD/WIND CHILL"
weather$EVTYPE[grepl("FUNNEL", weather$EVTYPE)] = "FUNNEL CLOUD"
weather$EVTYPE[!grepl("FREEZING|SNOW|LOW", weather$EVTYPE) & grepl("HEAVY|RECORD|EXCESSIVE|TORRENTIAL|HVY|PROLONGED|ABNORMALLY|EXTREMELY|UNSEASONAB", weather$EVTYPE) & grepl("RAIN|PRECIPITATION|WET", weather$EVTYPE)] = 'HEAVY RAIN'
weather$EVTYPE[!grepl("FREEZING|SNOW|LOW", weather$EVTYPE) & !grepl("HEAVY|RECORD|EXCESSIVE|TORRENTIAL", weather$EVTYPE) & grepl("RAIN|PRECIPITATION|WET", weather$EVTYPE)] = 'RAIN'
weather$EVTYPE[!grepl("RAIN|SLEET|STORM|LAKE", weather$EVTYPE) & grepl("ADVISORY|EXCESSIVE|RECORD|HEAVY", weather$EVTYPE) & grepl("SNOW", weather$EVTYPE)] = "HEAVY SNOW"
weather$EVTYPE[!grepl("LAKE", weather$EVTYPE) & !grepl("ADVISORY|EXCESSIVE|RECORD|HEAVY", weather$EVTYPE) & grepl("SNOW", weather$EVTYPE)] = "SNOW"
weather$EVTYPE[grepl("LAKE", weather$EVTYPE) & grepl("SNOW", weather$EVTYPE)] = 'LAKE-EFFECT SNOW'
weather$EVTYPE[grepl("FREEZING", weather$EVTYPE) & grepl("RAIN|DRIZZLE", weather$EVTYPE)] = 'SLEET'
weather$EVTYPE[grepl("SLEET", weather$EVTYPE)] = 'SLEET'
weather$EVTYPE[grepl("SURF", weather$EVTYPE)] = 'HIGH SURF'
weather$EVTYPE[grepl("TYPHOON|HURRICANE", weather$EVTYPE)] = 'HURRICANE (TYPHOON)'
weather$EVTYPE[grepl("ICE STORM", weather$EVTYPE)] = 'ICE STORM'
weather$EVTYPE[grepl("WINTER STORM", weather$EVTYPE)] = 'WINTER STORM'
weather$EVTYPE[grepl("LIGHTNING", weather$EVTYPE)] = 'LIGHTNING'
weather$EVTYPE[grepl("MARINE MISHAP", weather$EVTYPE)] = 'MARINE ACCIDENT'
weather$EVTYPE[grepl("MARINE TSTM WIND", weather$EVTYPE)] = 'MARINE THUNDERSTORM WIND'
weather$EVTYPE[grepl("RIP CURRENT", weather$EVTYPE)] = 'RIP CURRENT'
weather$EVTYPE[grepl("STORM SURGE|BLOW-OUT TIDE|COASTAL SURGE", weather$EVTYPE)] = 'STORM SURGE/TIDE'
weather$EVTYPE[grepl("TROPICAL STORM", weather$EVTYPE)] = 'TROPICAL STORM'
weather$EVTYPE[grepl("VOLCANIC ASH", weather$EVTYPE)] = 'VOLCANIC ASH'
weather$EVTYPE[grepl("FIRE", weather$EVTYPE)] = 'WILDFIRE'
weather$EVTYPE[grepl("WINTER WEATHER", weather$EVTYPE)] = 'WINTER WEATHER'
weather$EVTYPE[grepl("WINTER STORM|WINTERY MIX|WINTER MIX", weather$EVTYPE)] = 'WINTER STORM'
weather$EVTYPE[grepl("MICROBURST|MICOBURST", weather$EVTYPE)] = 'MICROBURST'
weather$EVTYPE[grepl("BLIZZARD", weather$EVTYPE)] = 'BLIZZARD'
weather$EVTYPE[grepl("THUNDER", weather$EVTYPE) & !grepl("WIND|SEVERE", weather$EVTYPE)] = 'THUNDERSTORM'
weather$EVTYPE[grepl("SLIDE", weather$EVTYPE)] = 'MUDSLIDE/LANDSLIDE/ROCKSLIDE'
weather$EVTYPE[grepl("WARM|HOT|HEAT", weather$EVTYPE)] = 'EXCESSIVE HEAT'
```


```r
weather_avg_deaths <- aggregate(weather$FATALITIES, by = list(EVTYPE = weather$EVTYPE), FUN = mean, na.rm = TRUE)
weather_avg_deaths<- weather_avg_deaths[order(-weather_avg_deaths$x),]
weather_sum_deaths <- aggregate(weather$FATALITIES, by = list(EVTYPE = weather$EVTYPE), FUN = sum, na.rm = TRUE)
weather_sum_deaths<- weather_sum_deaths[order(-weather_sum_deaths$x),]
weather_avg_dmg <- aggregate(weather$PROPDMG, by = list(EVTYPE = weather$EVTYPE), FUN = mean, na.rm = TRUE)
weather_avg_dmg<- weather_avg_dmg[order(-weather_avg_dmg$x),]
weather_sum_dmg <- aggregate(weather$PROPDMG, by = list(EVTYPE = weather$EVTYPE), FUN = sum, na.rm = TRUE)
weather_sum_dmg<- weather_sum_dmg[order(-weather_sum_dmg$x),]
weather_avg_inj <- aggregate(weather$INJURIES, by = list(EVTYPE = weather$EVTYPE), FUN = mean, na.rm = TRUE)
weather_avg_inj<- weather_avg_inj[order(-weather_avg_inj$x),]
weather_sum_inj <- aggregate(weather$INJURIES, by = list(EVTYPE = weather$EVTYPE), FUN = sum, na.rm = TRUE)
weather_sum_inj<- weather_sum_inj[order(-weather_sum_inj$x),]
weather_sum_deaths <- head(weather_sum_deaths, 5)
weather_avg_deaths <- head(weather_avg_deaths, 5)
weather_sum_dmg <- head(weather_sum_dmg, 5)
weather_avg_dmg <- head(weather_avg_dmg, 5)
weather_sum_inj <- head(weather_sum_inj, 5)
weather_avg_inj <- head(weather_avg_inj, 5)
```


```r
max_death_events <- weather_sum_deaths$EVTYPE
max_inj_events <- weather_sum_inj$EVTYPE
max_dmg_events <- weather_sum_dmg$EVTYPE
df_max_deaths <- weather[weather$EVTYPE %in% max_death_events,]
df_max_inj <- weather[weather$EVTYPE %in% max_inj_events,]
df_max_dmg <- weather[weather$EVTYPE %in% max_dmg_events,]
```


```r
p1 <- qplot(df_max_dmg$EVTYPE, df_max_dmg$PROPDMG, geom = "boxplot", main = "Top 5 Events with the Most Property Damage") + coord_flip() + xlab("Event Type") + ylab("Property Damage")
```

```r
p2 <- qplot(df_max_inj$EVTYPE, df_max_inj$INJURIES, geom = "boxplot", main = "Top 5 Types of Events with the Most Injuries") + coord_flip() + xlab("Event Type") + ylab("Number of Injuries per Recorded Event")
```


```r
p3 <- qplot(df_max_deaths$EVTYPE, df_max_deaths$FATALITIES, geom = "boxplot",  main = "Top 5 Types of Events with the Most Deaths") + coord_flip() + xlab("Event Type") + ylab("Number of Deaths per Recorded Event")
```


```r
p1 + p2 + p3 + plot_layout(ncol = 1)
```

![plot of chunk unnamed-chunk-67](figure/unnamed-chunk-67-1.png)