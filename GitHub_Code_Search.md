# GitHub Code Search

## Search Qualifiers

GitHub Code Search supports various search qualifiers to help you refine your search results. Here are some commonly used qualifiers:

### Main Qualifiers

`owner`, `org`, `user`, `repo`, `language`, `filename`, `path`, `type`, `base`, `reason`, `author`, `assignee`, `mentions`, `commenter`, `involves`, `linked`, `status`, `label`, `milestone`, `is`, `archived`, `fork`, `created`, `pushed`, `updated`, `size`, `stars`, `forks`, `topic`, `visibility`, `reviewed-by`, `review-requested`, `draft`, `merged`, `closed`, `state`

### Flex Qualifiers

- `type`: Specifies the type of item to search for.
    - Possible values include `code`, `commit`, `package`, `marketplace`, `issue`, `pr`, `repo`, `user`, etc.
    - Example: `type:code`
- `is`: Filters results based on specific states or conditions.
    - Possible values include `open`, `closed`, `merged`, `unmerged`, `public`, `draft`, `archived`, `fork`, etc.
    - Example: `is:open`
- `in`: Specifies the fields to search within.
    - Possible values include `title`, `body`, `comments`, etc.
    - Example: `in:title`
- `language`: Filters results by programming language.
    - Example: `language:python`
    - You can combine multiple languages using `OR`.
    - Example: `language:python OR language:javascript`
    - You can exclude a language using `-`.
    - Example: `-language:java`
- `created`, `pushed`, `updated`: Filters results based on date ranges.
    - You can use comparison operators like `>`, `<`, `>=`, `<=`.
    - Example: `created:>2022-01-01`
- `size`, `stars`, `forks`: Filters results based on numerical values.
    - You can use comparison operators like `>`, `<`, `>=`, `<=`.
    - Example: `stars:>100`
    - You can combine multiple numerical filters using `AND`.
    - Example: `stars:>100 forks:>50`
- `status`: Filters results based on the status of an item.
    - Possible values include `open`, `closed`, `merged`, etc.
    - Example: `status:open`
- `label`: Filters results based on labels assigned to issues or pull requests.
    - Example: `label:bug`
    - You can combine multiple labels using `AND`.
    - Example: `label:bug label:high-priority`
- `org`, `user`, `owner`: Filters results based on the organization, user, or owner of the repository.
    - Example: `org:github` OR `org:microsoft`
    - Example: `user:octocat`
    - Example: `owner:octocat`

### Sorting Results

You can sort your search results using the `sort` qualifier followed by the sorting criteria. Common sorting options include:

- `sort:stars`: Sorts results by the number of stars.
- `sort:forks`: Sorts results by the number of forks.
- `sort:updated`: Sorts results by the last updated date.
- `sort:created`: Sorts results by the creation date.
- `sort:best-match`: Sorts results by relevance (default).
- `sort:date`: Sorts results by date.
- `sort:date-asc`: Sorts results by date in ascending order.
- `sort:date-desc`: Sorts results by date in descending order.
- Example: `sort:stars`