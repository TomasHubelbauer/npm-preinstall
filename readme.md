# NPM `preinstall`

Does `preinstall` run only when you publish your package and someone is installing
it or does it also run in your own package when you run `npm install` to install
its dependencies?

## When being installed as a dependency

```sh
cd principal
npm i ..
# Prints the preinstall message
```

Works as expected.

## When installing its own dependencies

```
npm i
```

Not printing the `preinstall` message.

## Findings

The `preinstall` NPM hook is not a suitable solution to running checks
and tools before `npm install` is issued in a package directory.

This is unfortunate as it could enable scenarios such as checking private
repository credentials and system prerequisites as a part of the installation
process.

Without it, the only solution I am aware of is to document the fact that
a script needs to run before running `npm i`, which is subpar because it
is susceptible to human error (AKA forgetting to do it) or to distribute
a script to run instead of `npm i` which has portability challenges and is
still susceptible to the same problem.

From the point of view of developer experience, it would be really great
to have a hook to fill this void. However, NPM is unlikely to include such
quality of life improvements IMO.

## Update

There is also `prepublish` which gets run both on local NPM install and
before `publish`. There's some stuff that happened around this:

https://docs.npmjs.com/misc/scripts#prepublish-and-prepare

Long story short, use `prepare` for a non-deprecated hook which runs before
local NPM install, but keep in mind, that this is also called before publish.

This might be undesirable when using `prepare` for installing prerequisites
where they are not required for publish but are for running the package in
development.

It doesn't appear there is a hook which _only_ runs before local NPM
install.
