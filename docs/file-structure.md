# File Structure

> Note: Currently this document contains some choices that apply specifically to
> fontend Next.js + React repositories.

For anything but a trivial small-scale application I like to structure files
based on feature rather than type. This means grouping together several
different kinds of files that are strongly related as a result of some form of
interdependency.

Here are some reasons for doing so:

* Keeping related files together keeps folder contents manageable when the
  application scales. Files should be easier to locate, and naming things
  becomes easier as clashes within the same context are less likely to happen.
* By grouping files and only exporting a limited set of functionality, it
  becomes more apparent what the relationships are between parts of code, and
  you are likely spending more time thinking about "separation of concerns".
* Use of barrel-files is less problematic (some bundlers are not good at
  tree-shaking), since you are bundling code that is likely to be required
  together anyway.


## File and Folder Names

* Folders are named in plural form if possible. So prefer `utils` instead of
  `util`. Other examples are `domains`, `modules`, `components`, `exports`. An
  exception is `src` since that is such a common standard. Also it is an acronym
  and `sources` somehow doesn't feel right.
* File names are written in lower `kebab-case` or `snake_case`. I find this
  helps to keep things consitent especially when you export multiple things from
  a single file. @TODO elaborate

## Repo Root Level Structure

All sources in the root of the repository live in `src`, except when the
repository is set up as a monorepo, then each package has its source files in
`packages/[package-name]/src`.


## SRC Level Folders

These are some typical folders that live in the source directory:

* `domains`: The different features or subjects that make up the application and
  can be seen as independent parts. One domain should ideally not import files
  from another domain. If this happens consider moving those parts to a shared
  location outside of the domain folder like the ones described below. Some
  examples of folders in domains are `onboarding`, `flights`, `reviews`,
  `hotels`, `settings`. The root of this folder *should not have a barrel file*.
* `modules` or `services`: An group of logic functions that is used by one or
  more domains. Some examples are `firestore`, `logging`, `messaging`,
  `storage`. These could be helper functions around external APIs like Firebase,
  or some code that could potentially be released as a separate module on NPM.
  The root of this folder *should not have a barrel file*.
* `utils`: Very generic and simple utility functions that are likely to be used
  across several domains, components and modules. Using a barrel file in this
  folder would likely not be a problem.
* `components`: React components that are shared between different domains. If a
  component is. If you've used the domains as described, then this folder would
  mostly contain components that are used regularly, so you could consider using
  a barrel file here.
* `hooks`: React hooks that are shared between different components. Using a
  barrel file in this folder would likely not be a problem.

## Domain folder structure

A domain should have several subfolders to structure its contents. The case
described here is specific for a React application.

* `[root]`: Some files can live in the root of each domain folder. These
  typically form the public interface to that domain, and in the case of a
  Next.js application would include the page files. Alternatively you can choose
  to place these in a dedicated `pages` folder, but in my experience a domain
  doesn't typically span a lot of pages, so I find it more convenient to keep
  them in the root.
* `components`: Components specific to this domain. Also see [Recursive
  Component Structure](./file-structure#recursive-component-structure) below.
* `logic`: Logic functions and hooks specific to this domain. If there are only
  a few fuctions, this could be a single `logic.ts` file with named exports.
* `index.ts`: A barrel file exporting the public interface of this domain. In a
  Next.js application that would be the pages.

## Recursive Component Structure

A complex component would be made up of several sub-components, hooks and other
logic that are specific to that component. In order to keep these things
together I like to use a recursive structure. This recursion lets you break up a
complex component into its sub-components and logic that then could also have
their own sub-components and logic, making it scale indefinitely.

* `[component-name].tsx`: This would be the main component, and it should have
  the same name as the folder it lives in. Do not name this `index.tsx` as it
  makes it to navigate when the filename doesn't give a clue about the component
  it contains.
* `components`: The sub-components that are only used in this component. By
  co-locating sub-components you also don't need to make their names globally
  unique. In my view it would be perfectly fine to have mulitple components
  named `Header` if they are only used as sub-components and their names make
  sense in that context. So the whole problem of "naming things" should become a
  little easier when you're keeping things in their respective contexts.
* `components/index.ts`: A barrel file to export all sub-components for cleaner
  imports.
* `logic` or `logic.ts`: A folder or file containing the logic specific to this
  component. I also consider React hooks to be part of logic, as I see no good
  reason to make a distinction, but alternatively hooks could be grouped under
  their own `hooks` folder like the repo root. If a component only has very
  little logic functions I sometimes prefer to just use a `logic.ts` file with
  multiple named exports.
* `logic/index.ts`: A barrel file to export all logic for cleaner imports.
* `types.ts`: In general I think types are best kept with the code, but
  sometimes it feels convenient to have a types file in the root of the
  component.
* `index.ts`: This is the public interface. It should export the main component
  `[component-name].tsx` and possibly some types too.

