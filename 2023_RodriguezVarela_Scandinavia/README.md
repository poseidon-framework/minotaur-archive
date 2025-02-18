# 2023_RodriguezVarela_Scandinavia
This package was created on 2024-11-22 and was processed using the following versions:
 - nf-core/eager version:  2.5.1
 - Minotaur config version: 0.4.0dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.0dev
 - Minotaur-packager version: 0.4.2dev
 - populate_janno.py version: 0.4.1dev

## Fill relevant columns from provided janno.

```bash
## trident version: 1.5.4.0
remote_janno_url="https://raw.githubusercontent.com/poseidon-framework/community-archive/0e0b80386996f718bb6f825cae97467397ac6ad8/2023_Rodriguez_Varela/2023_RodriguezVarela_Scandinavia.janno"
source_janno="remote_janno.janno"
package_name="2023_RodriguezVarela_Scandinavia"
target_janno="${package_name}/${package_name}.janno"

## Get remote janno
curl -L ${remote_janno_url} -o ${source_janno}
## Provided janno has poseidon_IDs and Relationship_To in lowercase, while the ssf had them in uppercase.
awk -F "\t" '
  BEGIN{
    OFS=IFS="\t"
  }
  NR==1 {print}
  NR>1{
    $1=toupper($1)
    if ($4 != "n/a") { $4=toupper($4) }
    print
  }
  ' ${source_janno} > tmp.janno
mv tmp.janno ${source_janno}

## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ${source_janno} \
  -t ${target_janno} \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ${source_janno} \
  -t ${target_janno} \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Genetic_Sex,Group_Name \
  --force

## Mirror Sex and Group name info to the fam file.
paste -d "\t" ${package_name}/${package_name}.fam <(cut -f 1-3 ${package_name}/${package_name}.janno |tail -n +2) | \
  awk '
  BEGIN{
    OFS=IFS="\t"
  }
  {
    $1=$9
    if ($8 == "M") {
      $5=1
    } else if ($8 == "F") {
      $5=2
    }
    print $1,$2,$3,$4,$5,$6
  }
  ' > tmp.fam
## Cannot overwrite in the same command that reads in the file, so an extra mv is needed.
mv tmp.fam ${package_name}/${package_name}.fam

trident rectify --packageVersion Patch --logText "Fill-in metadata from remote-janno: ${remote_janno_url}" --checksumAll -d ${package_name}
```