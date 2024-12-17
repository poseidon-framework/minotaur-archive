Adds/Updates `<package_name>`

Linked to https://github.com/poseidon-framework/minotaur-recipes/issues/XXX
<!-- Link to the Minotaur-recipes issue above. https://github.com/poseidon-framework/minotaur-recipes/issues -->

New packages added to the [minotaur archive](https://github.com/poseidon-framework/minotaur-archive) are always published in version `1.0.0`. This means that nay and all changes made to the package during the review process, should only bump the `Minor` or `Patch` version of the package.

PR checklist:

- [ ] This package contains a BibTex file.
 - If this package already exists in the [community archive](https://github.com/poseidon-framework/community-archive):
   - [ ] The information from the community archive was used to fill-in empty janno fields.
   - [ ] The commands used for the fill-in and any tool versions are kept track of in the README file.
   - [ ] The BibTex key used for the citation is _identical_ to that in the corresponding package in the community archive.
   - [ ] The DOI in the BibTex file is _identical_ to the one used in the corresponding package in the comunity archive.
- [ ] `Country` information is also present in the `Country_ISO` column.
- [ ] Add any additional contributors to `POSEIDON.yml`

Once the Pull Request has passed the review process, ensure the package version is bumped once more before publishing with:

```
trident rectify -d <package_dir> --logText 'Bump version for release' --packageVersion Major --checksumAll
```
