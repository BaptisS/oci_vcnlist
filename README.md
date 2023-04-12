# oci_vcnlist


````
#!/bin/sh
rm -f vcnlist_allregions.log

#Region-1
export region="region"

rm -f vcnlist-$region.log
rm -f vcnlistfull-$region.log
rm -f vcnlistdetailed-$region.log

oci search resource structured-search --query-text "query vcn resources" --region $region >> vcnlist-$region.log
vcnlistcur=$(cat vcnlist-$region.log | jq .data |  jq .items | jq -r '.[] | ."identifier"')

for vcn in $vcnlistcur; do oci network vcn get --vcn-id $vcn --region $region >> vcnlistfull-$region.log ; done
echo "LM, Display Name, DNS Label, ocid, CIDR Block(s)" > vcnlistdetailed-$region.log
cat vcnlistfull-$region.log | jq -r '.[] | [."freeform-tags"."lm", ."display-name", ."dns-label", ."id", ."cidr-blocks"[]] | @csv' >> vcnlistdetailed-$region.log

cat vcnlistdetailed-$region.log >> vcnlist_allregions.log
````

