# 2021_PattersonNature

This package was created on 2025-09-04 and was processed using the following versions:

- nf-core/eager version: 2.5.1
- Minotaur config version: 0.5.0
- CaptureType profile: 1240K
- CaptureType config version: 0.2.2dev
- Config template version: 0.5.0
- Package config version: 0.5.0
- Minotaur-packager version: 0.5.0
- populate_janno.py version: 0.5.1

## Fill relevant columns from the community archive package.

```bash
package_name="2021_PattersonNature"
regex=("(_ss_MNT$)|(_MNT$)|(\.AG$)|(_d$)|(_d\.AG$)" "(_ss_MNT$)|(_MNT$)|(_d\.AG$)")

## The AADR is the de-facto source of metadata for this package, so we start with that.
########################
## EuropeAncientNorth ##
########################
aadr_archive_package="AADR_v62_1240K_EuropeAncientNorth"
aadr_package_version="1.1.0"
aadr_janno_fn=$(readlink -f ../../archives/aadr-archive/${aadr_archive_package}-${aadr_package_version}/*.janno)

## First fill-in missing metadata from relevant columns (no processing-based info).
## This is ran for different regexes because some IDs exist with multiple suffixes which crash the script if picked up together.
for rgx in "${regex[@]}"; do
  trident jannocoalesce \
    -s ${aadr_janno_fn} \
    -t ${package_name}/${package_name}.janno \
    --stripIdRegex ${rgx} \
    --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

  ## Then fill in Group_Name and Genetic_Sex
  trident jannocoalesce \
    -s ${aadr_janno_fn} \
    -t ${package_name}/${package_name}.janno \
    --stripIdRegex ${rgx} \
    --includeColumns Genetic_Sex,Group_Name \
    --force
done
###################
## BeyondAncient ##
###################
aadr_archive_package="AADR_v62_1240K_BeyondAncient"
aadr_package_version="1.0.0"
aadr_janno_fn=$(readlink -f ../../archives/aadr-archive/${aadr_archive_package}-${aadr_package_version}/*.janno)

## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ${aadr_janno_fn} \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex ${regex[0]} \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ${aadr_janno_fn} \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex ${regex[0]} \
  --includeColumns Genetic_Sex,Group_Name \
  --force

########################
## EuropeAncientSouth ##
########################
aadr_archive_package="AADR_v62_1240K_EuropeAncientSouth"
aadr_package_version="1.1.0"
aadr_janno_fn=$(readlink -f ../../archives/aadr-archive/${aadr_archive_package}-${aadr_package_version}/*.janno)

## First fill-in missing metadata from relevant columns (no processing-based info).
for rgx in "${regex[@]}"; do
  trident jannocoalesce \
    -s ${aadr_janno_fn} \
    -t ${package_name}/${package_name}.janno \
    --stripIdRegex ${rgx} \
    --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

  ## Then fill in Group_Name and Genetic_Sex
  trident jannocoalesce \
    -s ${aadr_janno_fn} \
    -t ${package_name}/${package_name}.janno \
    --stripIdRegex ${rgx} \
    --includeColumns Genetic_Sex,Group_Name \
    --force
done

#######################
## community archive ##
#######################
## No metadata is available on the AADR for 9 individuals. Some are found in the community archive instead.
community_archive_version="0.4.3"
community_janno_fn=$(readlink -f ../../archives/community-archive/${package_name}-${community_archive_version}/*.janno)
regex_community="(_ss_MNT$)|(_MNT$)"

## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ${community_janno_fn} \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex ${regex_community} \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ${community_janno_fn} \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex ${regex_community} \
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

  ## trident version: 1.5.4.0
  trident rectify --packageVersion Patch --logText "Fill-in metadata from community-archive: ${package_name}-${community_archive_version} AADR_v62_1240K_EuropeAncientNorth-1.1.0 AADR_v62_1240K_BeyondAncient-1.0.0 AADR_v62_1240K_EuropeAncientSouth-1.1.0" --checksumAll -d ${package_name}
```
