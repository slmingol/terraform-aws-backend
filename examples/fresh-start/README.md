## terraform-aws-backend fresh start example

In this example, we assume no existing backend resources. That is,
we will create our s3 bucket(s) and dynamodb lock table from
scratch without doing any `terraform import`s.

To run this example:

```
$ terraform get -update
$ terraform init -backend=false
$ terraform plan -out=backend.plan -target=module.backend -var "backend_bucket=tf-backend-aws-example"
$ terraform apply backend.plan
$ echo 'terraform { backend "s3" {} }' > conf.tf
$ terraform init -reconfigure -backend-config=conf.tfvars

# Now 'show' should show your terraform backend resource attributes
$ terraform show
```

**NOTE:** Be sure to change the name of the `backend_bucket=XXXX` otherwise your bucket creation will fail due to the name above already being used by the original author.

## Example Run
```
$ terraform apply backend.plan
module.backend.aws_s3_bucket.tf_backend_logs_bucket: Creating...
  acceleration_status:         "" => "<computed>"
  acl:                         "" => "log-delivery-write"
  arn:                         "" => "<computed>"
  bucket:                      "" => "bw-tf-backend-aws-example-logs"
  bucket_domain_name:          "" => "<computed>"
  bucket_regional_domain_name: "" => "<computed>"
  force_destroy:               "" => "false"
  hosted_zone_id:              "" => "<computed>"
  region:                      "" => "<computed>"
  request_payer:               "" => "<computed>"
  tags.%:                      "" => "3"
  tags.ManagedByTerraform:     "" => "true"
  tags.Purpose:                "" => "Logging bucket for bw-tf-backend-aws-example"
  tags.TerraformModule:        "" => "terraform-aws-backend"
  versioning.#:                "" => "1"
  versioning.0.enabled:        "" => "true"
  versioning.0.mfa_delete:     "" => "false"
  website_domain:              "" => "<computed>"
  website_endpoint:            "" => "<computed>"
module.backend.aws_dynamodb_table.tf_backend_state_lock_table: Creating...
  arn:                       "" => "<computed>"
  attribute.#:               "" => "1"
  attribute.2068930648.name: "" => "LockID"
  attribute.2068930648.type: "" => "S"
  billing_mode:              "" => "PROVISIONED"
  hash_key:                  "" => "LockID"
  name:                      "" => "terraform-lock"
  point_in_time_recovery.#:  "" => "<computed>"
  read_capacity:             "" => "1"
  server_side_encryption.#:  "" => "<computed>"
  stream_arn:                "" => "<computed>"
  stream_enabled:            "" => "false"
  stream_label:              "" => "<computed>"
  stream_view_type:          "" => "<computed>"
  tags.%:                    "" => "3"
  tags.Description:          "" => "Terraform state locking table for account XXXXXXXXXX."
  tags.ManagedByTerraform:   "" => "true"
  tags.TerraformModule:      "" => "terraform-aws-backend"
  write_capacity:            "" => "1"
module.backend.aws_s3_bucket.tf_backend_logs_bucket: Creation complete after 10s (ID: bw-tf-backend-aws-example-logs)
module.backend.aws_s3_bucket.tf_backend_bucket: Creating...
  acceleration_status:                                                                                   "" => "<computed>"
  acl:                                                                                                   "" => "private"
  arn:                                                                                                   "" => "<computed>"
  bucket:                                                                                                "" => "bw-tf-backend-aws-example"
  bucket_domain_name:                                                                                    "" => "<computed>"
  bucket_regional_domain_name:                                                                           "" => "<computed>"
  force_destroy:                                                                                         "" => "false"
  hosted_zone_id:                                                                                        "" => "<computed>"
  logging.#:                                                                                             "" => "1"
  logging.1978772297.target_bucket:                                                                      "" => "bw-tf-backend-aws-example-logs"
  logging.1978772297.target_prefix:                                                                      "" => "log/"
  region:                                                                                                "" => "<computed>"
  request_payer:                                                                                         "" => "<computed>"
  server_side_encryption_configuration.#:                                                                "" => "1"
  server_side_encryption_configuration.0.rule.#:                                                         "" => "1"
  server_side_encryption_configuration.0.rule.0.apply_server_side_encryption_by_default.#:               "" => "1"
  server_side_encryption_configuration.0.rule.0.apply_server_side_encryption_by_default.0.sse_algorithm: "" => "AES256"
  tags.%:                                                                                                "" => "3"
  tags.Description:                                                                                      "" => "Terraform S3 Backend bucket which stores the terraform state for account XXXXXXXXXX."
  tags.ManagedByTerraform:                                                                               "" => "true"
  tags.TerraformModule:                                                                                  "" => "terraform-aws-backend"
  versioning.#:                                                                                          "" => "1"
  versioning.0.enabled:                                                                                  "" => "true"
  versioning.0.mfa_delete:                                                                               "" => "false"
  website_domain:                                                                                        "" => "<computed>"
  website_endpoint:                                                                                      "" => "<computed>"
module.backend.aws_dynamodb_table.tf_backend_state_lock_table: Still creating... (10s elapsed)
module.backend.aws_dynamodb_table.tf_backend_state_lock_table: Creation complete after 13s (ID: terraform-lock)
module.backend.aws_s3_bucket.tf_backend_bucket: Still creating... (10s elapsed)
module.backend.aws_s3_bucket.tf_backend_bucket: Creation complete after 11s (ID: bw-tf-backend-aws-example)
module.backend.data.aws_iam_policy_document.tf_backend_bucket_policy: Refreshing state...
module.backend.aws_s3_bucket_policy.tf_backend_bucket_policy: Creating...
  bucket: "" => "bw-tf-backend-aws-example"
  policy: "" => "{\n  \"Version\": \"2012-10-17\",\n  \"Statement\": [\n    {\n      \"Sid\": \"RequireEncryptedTransport\",\n      \"Effect\": \"Deny\",\n      \"Action\": \"s3:*\",\n      \"Resource\": \"arn:aws:s3:::bw-tf-backend-aws-example/*\",\n      \"Principal\": \"*\",\n      \"Condition\": {\n        \"Bool\": {\n          \"aws:SecureTransport\": \"0\"\n        }\n      }\n    },\n    {\n      \"Sid\": \"RequireEncryptedStorage\",\n      \"Effect\": \"Deny\",\n      \"Action\": \"s3:PutObject\",\n      \"Resource\": \"arn:aws:s3:::bw-tf-backend-aws-example/*\",\n      \"Principal\": \"*\",\n      \"Condition\": {\n        \"StringNotEquals\": {\n          \"s3:x-amz-server-side-encryption\": \"AES256\"\n        }\n      }\n    }\n  ]\n}"
module.backend.aws_s3_bucket_policy.tf_backend_bucket_policy: Creation complete after 1s (ID: bw-tf-backend-aws-example)

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

dynamodb_lock_stream_arn = [

]
dynamodb_lock_stream_label = [

]
dynamodb_lock_table_arn = [
    arn:aws:dynamodb:us-west-2:XXXXXXXXXX:table/terraform-lock
]
dynamodb_lock_table_name = [
    terraform-lock
]
s3_backend_bucket_name = [
    bw-tf-backend-aws-example
]
```
