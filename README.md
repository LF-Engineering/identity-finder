# identity-finder
Find possible identity fields per data source type and columns values (optional)

# [identities.yaml](https://github.com/LF-Engineering/identity-finder/blob/main/identities.yaml)

This [file](https://github.com/LF-Engineering/identity-finder/blob/main/identities.yaml) describes how to find possible identities for a given data source type.

It has two sections:

- `datasources` - describes how to find identity data in a given data source type.
- `default` - default list to fall-back when a given data source configuration cannot be found.


# Default section

This contains a list of all possible `identity` field candidates across all data source types.

If the identity is named, say `user` you can possibly find identity data in fields like: `user_id`, `user_uuid`, `user_bot`, `user_name`, `user_org_name`, `user_multi_org_names` etc.

If the identity name contains a suffix `_[2-N]` - it means that a given identity is a so-called `multi` identity, for example `recipient_[2-50]`.

It means that there can actually be up to 50 idenity fields with that name, for example `recipient`, `recipient_2`, ..., `recipient_50` (up to 50 can also mean 10, 2 1 or none).

Actual possible identity data fields would be: `recipient_uuid`, `recipient_2_id`, `recipient_50_org_name` etc.


# Datasources section

Property name is the actual data source name, so properies can be: `git`, `gerrit`, `github`, `confluence` etc.

Each property can have 4 arrays - all are optional, they are:

- `identities`: keeps a list of possible identity fields, like `[author, reporter]`, if that array is empty `[]` or not specified - it means that a given data source has no identities data (for example `github/repository`).
- `columns`: for some datasources (like `gerrit`, `github`) list of possible identity columns depends on which kind of document we're looking at, here we can specify columns to distinguish different document types, most often this contains `type` and/or `category` column(s), example: `[category, type]`. If `columns` is empty (`[]`) or not specified - it means all documents have the same structure in this data source type.
- `values`: must be used with `columns` to specify expected columns values, so it must have the same number of elements as `columns`, example combination to specify GitHub PR reviews: `columns:[category, type], values:[pull_request, pull_request_review]` or gerrit approval: `columns:[type], values:[approval]`.
- `max`: specifies if some of `identities` fiels are so called multi-value fields or not, that array must eiter have the same number as `identities` array or be empty (we are assuming as if it has value `1` for all `identities` array entries). For each identity this value can be: `1` (single item), `0` (means the same as 1), `-1`: unlimited, `N`. Specifying for example for grups.io: `identities:[author, recipient]` and `max:[1,50]` means that actual identity candidates are: `author` (single) and `recipient`, `recipient_2`, ..., `recipient_50` (up to 50 recipients).
