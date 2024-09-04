# Package

Package files enable you to define additional Git repositories whose
objects can be used in the current repository. This enables you to share
individual objects (such as dimensions) across multiple models.

For more information on working with packages, see [Working with
Packages](../../c-creating-and-sharing-cubes/working-with-git/working-with-packages.md).

Package files support the following properties.

## version

- **Type:** number
- **Required:** Y

The schema version for the file. The value of this property should be
`1`.

## packages

- **Type:** array
- **Required:** Y

A list of the Git repositories that the current repository can use
objects from.

`packages` supports the following properties:

- `name`: String, required. The name of the repository.
- `url`: String, required. The URL for the repository.
- `branch`: String, required. The specific branch from the repository to
  use.
- `version`: String, required. The ID for a specific commit to use. This
  should begin with `commit:` followed by 8-40 alphanumeric characters.