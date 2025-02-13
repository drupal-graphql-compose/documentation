# File Upload mutation

> :fire: Enable the **graphql_compose_file_upload** module.

This is a plug and play Mutation that allows users to upload **files** (blob) to Drupal through the GraphQL API.

- There are 2 input arguments. A file upload and the metadata.
- In order to upload the file to the correct paths and with the appropriate instance settings (e.g. available extensions, max limit etc) we need to declare the field instance metadata on the input. So uploading validations are managed from that metadata.
- When there are validation or other issues we get them to the errors value.
- The file on the input must be from a multipart form ([Multipart HTTP Request](https://github.com/jaydenseric/graphql-multipart-request-spec)).
- The response contains the new Drupal file entity on the results.values field.
When uploading a new file by default it is marked as **Temporary** in the Drupal database. When you attach it on another entity it is considered **Permanent**.
- The Drupal files with Temporary status cannot be Queried. So you can only get their results on the initial `fileUpload` mutation request.
- The Termporary files get cleaned from the system (by default every 6 hours).
- Uploads with the same filename create new files. There are no overrides.
- The permissions to create a new file with this mutation is from current logged in user. You cannot pass another uid.

## GraphQL definitions

```graphql
extend type Mutation {
  """
  File upload mutation
  """
  fileUpload(file: Upload!, metadata: fieldMetadata!): AnyFileUploadResponse
}

"""
The File uploaded. See specs at https://github.com/jaydenseric/graphql-multipart-request-spec.
"""
scalar Upload

"""
The response of the fileUpload mutation. Returns a File entity.
"""
type AnyFileUploadResponse {
  errors: [Violation]
  results: AnyEntity
}
```

## Mutation example: fileUpload

```graphql
# Example. Upload a file to the field "field_files" with settings from node type "event"
mutation ($file: Upload!) {
  fileUpload(
    file: $file,
    metadata: {
      field_name: "field_files",
      entity_type: NODE,
      entity_bundle: "event"
    }
  ) {
    errors
    results {
      entity_bundle
      entity_type
      id
      values
    }
  }
}
```

## Future plan

- Support multiple uploads in single Mutation.
- Allow to upload file from a remote URL.
- Return more structured entity types instead of the `AnyEntity` type (e.g. return an `Image` type with the `image_styles` values if the file is an image on the same response).
- Check how to use the addCacheableDependency better to optimize cache.
- Validate the NEW/UPDATED file.
- Add debugging options (through a UI checkbox).
- Add testing for the Mutation API calls (get Errors, get Results etc)
