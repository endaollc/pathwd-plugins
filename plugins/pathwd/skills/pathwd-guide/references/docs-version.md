# Docs version

This plugin's skills carry PATH docs version `9e85816adf2d`: the same guides, workflows and command pages the PATH WD connector's `path_docs` tool served when the plugin was built.

The connector reports the docs version it serves in the session context it returns (on `use-account`, `session-context` and the first command of a session), in the line "PATH docs version on this server: <version>."

When the two versions are the same, these files and `path_docs` hold the same text. When they differ, `path_docs` has the newer text: `path_docs(name: "<guide>")`, `path_docs(command: "<command>")` and `path_docs(query: "<words>")` answer from the server's current docs, and this plugin's copy catches up with its next release.
