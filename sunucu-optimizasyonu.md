# Sunucu Optimizasyonu

## HW RAID

Disk Cache Aktivasyonu

```bash
/opt/MegaRAID/MegaCli/MegaCli64 -LDGetProp -DskCache -LAll -aALL
```

Write-Cache aktivasyonu (RAID Pili yoksa elektrik kesintisinde bilgi kaybına neden olabilir)

```bash
/opt/MegaRAID/MegaCli/MegaCli64 -LDSetProp CachedBadBBU -LALL -aALL  

#Set Write Cache OK if bad BBU on Adapter 0, VD 0 (target id: 0) success
#Set Write Cache OK if bad BBU on Adapter 0, VD 1 (target id: 1) success

#Exit Code: 0x00
```
