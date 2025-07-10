# IoT Field Kernel Snaps

This repository is maintained by the IoT Devices Field team to store and test
kernel snaps we create.

Note that the contents of these kernel snaps are all public information and are
generally created for testing or interest in particular hardware. No promises
are made for stability or long term support.

Kernel snaps are generally verboten from the Global Store, so you will not
find these published anywhere. They are intended as implementation examples and
inspiration, for use on dangerous-grade model Ubuntu Core images.

* You can find IoT Field's gadget snaps [at this sister repository](https://github.com/canonical/iot-field-gadget-snap)
* You can find IoT Field's example snaps [at this cousin repository](https://github.com/canonical/iot-field-example-snaps)


## Repository structure

The branches correspond to a Core release and any platforms which have had a
kernel snap made for them can be found in that branch under their respective
subdirectory.

* The `main` branch is for hosting workflows, a project description (this README),
  and an example skeleton of a kernel snap.
* If you wish to add a new kernel to this repository, create a subdirectory for
  the platform on the relevant branch.
* If you wish to update a kernel snap from one base to another, please switch to
  the branch corresponding to the targetted release and checkout the platform's
  subdirectory from the most recent branch. For example:

  ```sh
    git checkout 24
    git checkout 22 -- nezha
    # proceed
  ```

## Subdirectories

Please ensure that your subdirectory includes a README describing:
 - the board,
 - how the kernel snap functions (in broad, general terms; no need to be too technical),
 - including references to documentation if you prefer

## Workflows

The expectation is that any new subdirectory should have an accompanying new
workflow added. That workflow should follow the general style of the other
workflows, and ideally there are two:

1) One workflow testing builds using some version-specific snapcraft (e.g. `7.x/edge`)
2) One workflow testing builds using snapcraft from `latest/edge`

This ensures continuous testing of our work to spot any potential regressions or breaking changes.

The workflows should only exist on the `main` branch.

## Contributing

This repository is open to contributions so long as:

1) You have signed the Canonical [Contributor License Agreement](https://ubuntu.com/legal/contributors)
2) You have signed the Ubuntu [Code of Conduct](https://launchpad.net/codeofconduct)

Commits should follow the below style:

* The entire message should read as:

```
    <subdirectory>: <path/to/file>: <imperative present tense short description of change>

    <longer explanation of change if required>

    Signed-off-by: <author name> <author email>
```

* Try to keep first line summaries to under 60 characters, body summaries under 80

* Include a signoff for each commit using `git commit -s`
    * To amend prior commits with a signoff, do `git rebase --signoff HEAD~<number of commits>`

* Signing commits with `git commit -S` (GPG or SSH) is preferred but not required

* If you are modifying the `README.md`, `LICENSE`, `.gitignore`, `.github`,
    etc., please use `README`, `LICENSE`, `github`, `gitignore`, etc as the filename in the message

* Commits should be atomic

* PRs should contain logically related commits

* Try to follow prior art and style wherever possible

Even if you have write permissions to this repository, unless it is trivial
please create a PR for your change and attempt to get a +1 from someone on the
FE IoT team.
