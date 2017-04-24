# Influx Db notes
## Posting data with curl
```bash
curl -i -POST "http://localhost:8086/write?db=icinga2" --data-binary 'annotations,title=recalibration,tags=server text="Recalibrated A/C to 14/24/24" '"$(date -d 'last thursday 3:15pm' +%s)000000000"
```

