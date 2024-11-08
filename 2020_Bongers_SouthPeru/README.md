# 2020_Bongers_SouthPeru-2.0.0
This package was updated on 2024-11-05 and was processed using the following versions:
 - nf-core/eager version:  2.5.1
 - Minotaur config version: 0.4.0dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.0dev
 - Minotaur-packager version: 0.4.2dev
 - populate_janno.py version: 0.4.1dev

## CHANGELOG:
 - Fixed a problem with the processing of PE sequenced data from ssDNA libraries [minotaur-recipes #53](https://github.com/poseidon-framework/minotaur-recipes/issues/53)
   - New genotypes were generated.
   - Data processing statistics were updated in the janno file to match latest processing.

## Fill metadata from the minotaur archive package (2020_Bongers_SouthPeru-1.0.0).

```bash
package_name="2020_Bongers_SouthPeru"
fill_from_archive="minotaur-archive"
fill_from_package="2020_Bongers_SouthPeru-1.0.0"
## trident v1.5.4.0
## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ../../archives/${fill_from_archive}/${fill_from_package}/2020_Bongers_SouthPeru.janno \
  -t 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ../../archives/${fill_from_archive}/${fill_from_package}/2020_Bongers_SouthPeru.janno \
  -t 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Genetic_Sex,Group_Name \
  --force

## Mirror Sex and Group name info to the fam file.
paste -d "\t" 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.fam <(cut -f 1-3 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno |tail -n +2) | \
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
mv tmp.fam 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.fam

## trident v1.5.4.0
trident rectify --checksumAll -d 2020_Bongers_SouthPeru/

## Arrange Poseidon_IDs alphabetically
## First, move the old directory out of the way (avoids all files being renamed by trident forge)
cd ../ ## Start from the parent directory, since we need to rename the directory we were in.
mv ${package_name} ${package_name}_old

##   qjanno v1.0.0.1
qjanno "SELECT '<'||Poseidon_ID||'>' FROM d(${package_name}_old) ORDER BY Poseidon_ID" --raw --noOutHeader > desiredOrder.txt

## Rearrange package contents to the desired order.
## trident v1.5.4.0
trident forge -d ${package_name}_old --forgeFile desiredOrder.txt -o ${package_name} --ordered --preservePyml
trident rectify -d ${package_name} --packageVersion Major --logText "Updated package contents due to reprocessing of sequence data" --checksumAll
## Once the new package has been verified, the '_old' directory can be removed.
```

# 2020_Bongers_SouthPeru-1.0.0
This package was created on 2024-05-06 and was processed using the following versions:
 - nf-core/eager version:  2.4.6
 - Minotaur config version: 0.2.1dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.0dev
 - Minotaur-packager version: 0.3.0dev
 - populate_janno.py version: 0.3.2dev

## Fill relevant columns from the community archive package.

```bash
## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ../../archives/community-archive/2020_Bongers_SouthPeru-2.2.0/Bongers_SouthPeru.janno \
  -t 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ../../archives/community-archive/2020_Bongers_SouthPeru-2.2.0/Bongers_SouthPeru.janno \
  -t 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Genetic_Sex,Group_Name \
  --force

## Mirror Sex and Group name info to the fam file.
paste -d "\t" 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.fam <(cut -f 1-3 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.janno |tail -n +2) | \
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
  mv tmp.fam 2020_Bongers_SouthPeru/2020_Bongers_SouthPeru.fam

  ## trident version: 1.4.1.0
  trident rectify --packageVersion Patch --logText "Fill-in metadata from community-archive: 2020_Bongers_SouthPeru-2.2.0" --checksumAll -d 2020_Bongers_SouthPeru/

## Arrange Poseidon_IDs alphabetically
## First, move the old directory out of the way (avoids all files being renamed by trident forge)
cd ../ ## Start from the parent directory, since we need to rename the directory we were in.
package_name="2020_Bongers_SouthPeru"
mv ${package_name} ${package_name}_old

##   qjanno v1.0.0.1
qjanno "SELECT '<'||Poseidon_ID||'>' FROM d(${package_name}_old) ORDER BY Poseidon_ID" --raw --noOutHeader > desiredOrder.txt

## Rearrange package contents to the desired order.
## trident v1.5.4.0
trident forge -d ${package_name}_old --forgeFile desiredOrder.txt -o ${package_name} --ordered --preservePyml
trident rectify -d ${package_name} --packageVersion Minor --logText "Arranged Poseidon_IDs alphabetically" --checksumAll
## Once the new package has been verified, the '_old' directory can be removed.
```
