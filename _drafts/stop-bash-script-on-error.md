---
title: "Stop Bash Scripts On Error"
---

**TL;DR**: _Don't forget to stop the execution of your bash scripts when an error occurs, e.g. by adding "``set -e -o pipefail``"._


### The Problem ###

If not handled explicitly, bash scripts don't stop automatically when a called command fails. 
If ignored, this behavior can have serious consequences, e.g. in a deployment script:

    #!/usr/bin/env bash
    
    ./prepare-assets-of-new-version.sh
    # This will be executed, even if the preparation of the assets fails.
    ./activate-new-version.sh

### Stop on Failure ###

The error handling can be modified by adding ``set -e`` at the beginning of the script:

    #!/usr/bin/env bash
    
    # Stop on failure.
    set -e
    
    ./prepare-assets-of-new-version.sh
    # The script does *not* reach this line, if the asset preparation fails.
    ./activate-new-version.sh

The ``set -e`` directive ensures that the script stops whenever a command ends with a non-zero exit code.

### Handling Commands in Pipes ###

Even with ``set -e``, there is a pitfall when pipes are used:

    #!/usr/bin/env bash
    
    set -e
    
    ./prepare-assets-of-new-version.sh | ./log-output.sh
    # This line is reached *even* if the asset preparation fails.
    ./activate-new-version.sh

When multiple commands are piped together, only the exit code of the *last* one is checked per default. 
For the example above that means, even if the asset preparation fails, the script will proceed as long as the ``log-output.sh`` command succeeds with an exit code of zero.

To address that problem, add the argument ``-o pipefail`` to the ``set`` command:

    #!/usr/bin/env bash
    
    # Stop even, if a command in a pipe fails.
    set -e -o pipefail
    
    ./prepare-assets-of-new-version.sh | ./log-output.sh
    ./activate-new-version.sh

Now the script stops if *any* command in a pipe chain fails.

### Final Thoughts ###

A bash script that does not stop when a statement fails bears some risk and might backfire in the future. Therefore, it might be a good idea to make it a habit to add ``set -e -o pipefail`` as one of the first statements in bash scripts.

I am by far no bash expert and there might be more sophisticated solutions to handle errors in bash, but this one captivates by its simplicity and saved me more than once.
