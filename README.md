<!---
{
  "id": "bae22056-8b3c-4c71-807d-68abc7171b36",
  "depends_on": ["e8add8e9-7a67-4b50-af89-6c1ce6558e0d"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-06-03",
  "keywords": ["jq", "json", "curl", "github api", "data extraction"]
}
--->

# Extracting and Filtering JSON Data with `jq`

> In this exercise you will learn how to use `jq` to parse and manipulate JSON data from both local files and web API calls. Furthermore we will explore how to combine `curl` and `jq` to query the GitHub API and extract specific repository information from the STEMgraph organization.

## Introduction

Modern APIs often return data in JSON format: a lightweight, human-readable data interchange format that is easy for computers to parse but can be quite dense for humans to read directly in raw form. While `curl` allows you to retrieve this data from APIs, processing and extracting meaningful information from the JSON output is where `jq` comes in.

`jq` is a powerful command-line JSON processor. It allows you to filter, transform, and query JSON data using a simple and expressive query language. Instead of manually parsing JSON with text-processing tools like `grep` or `awk`, `jq` understands JSON natively and allows you to address keys, arrays, and nested structures directly.

In real-world scenarios, you'll frequently combine `curl` and `jq` to access web APIs and extract only the data you care about. For example, many public APIs—including GitHub's—provide extensive information about projects, users, and organizations in JSON format. The GitHub REST API allows anyone to retrieve public repository data with minimal authentication. Today, we will use it to investigate the public repositories of the STEMgraph organization.

Understanding how to manipulate this data using `jq` is an essential skill for developers, data scientists, and system administrators who deal with APIs, configuration files, or log data. In this exercise, we will work step-by-step, starting with simple local JSON files before moving on to live data from the internet.

### Further Readings and Other Sources

* [jq Manual](https://stedolan.github.io/jq/manual/)
* [GitHub REST API Documentation](https://docs.github.com/en/rest)
* [JSON Data Format (RFC 8259)](https://doi.org/10.17487/RFC8259)
* [YouTube: jq Tutorial for Beginners](https://www.youtube.com/watch?v=clkwvXkQ3ew)
* [Understanding JSON: json.org](https://www.json.org/json-en.html)

## Tasks

### 0. Verify that `jq` is installed

Check whether `jq` is installed on your system:

```bash
jq --version
```

If it is not installed, you can install it using your package manager. On Debian-based systems (Ubuntu, etc.):

```bash
sudo apt update && sudo apt install jq
```

### 1. Practice on a local JSON file

Create a file named `sample.json` with the following content:

```json
{
  "user": "alice",
  "active": true,
  "roles": ["admin", "editor"],
  "last_login": "2025-06-01T15:43:21Z",
  "profile": {
    "age": 29,
    "country": "DE"
  }
}
```

Now use `jq` to extract individual fields:

**a. Extract the username:**

```bash
jq '.user' sample.json
```

**b. Extract the country:**

```bash
jq '.profile.country' sample.json
```

**c. Extract whether the user is active:**

```bash
jq '.active' sample.json
```

**d. List all roles:**

```bash
jq '.roles[]' sample.json
```

### 2. Query the GitHub API with `curl` and `jq`

#### a. Retrieve the repository list of the STEMgraph organization:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos
```

This will return an array of repository objects. The `-s` option suppresses progress output.

#### b. Pipe the output through `jq` to extract only the repository names:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos | jq '.[].name'
```

#### c. Extract both name and description for each repository:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos | jq '.[] | {name: .name, description: .description}'
```

#### d. Filter repositories that are forks:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos | jq '.[] | select(.fork == true) | .name'
```

#### e. (Optional Challenge) Extract repository names that were updated in 2024:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos | jq '.[] | select(.updated_at | startswith("2024")) | .name'
```

### 3. Save API output to a file for offline analysis

You can save the full API response into a file for later inspection:

```bash
curl -s https://api.github.com/orgs/STEMgraph/repos -o stemgraph_repos.json
```

Then, you can apply `jq` repeatedly on this local file:

```bash
jq '.[].name' stemgraph_repos.json
```

## Questions

1. What is the main advantage of using `jq` instead of `grep` or `awk` when processing JSON?
2. How does piping `curl` into `jq` help with working on live API data?
3. What happens if you try to select a non-existing field with `jq`?
4. What HTTP response code would you get if you query a private GitHub organization without authentication?
5. How would you modify your `curl` request if the API requires authentication?
6. Can `jq` handle nested JSON arrays? Give an example.
7. What is the meaning of the `select` function inside `jq` filters?
8. How would you extract only the repository names where the language is set to `Python`?

## Advice

The combination of `curl` and `jq` is extremely powerful when exploring or automating interactions with modern APIs. Rather than downloading and manually inspecting large JSON files, you can immediately extract the relevant data fields you're interested in. This is especially helpful when working with dynamic or large datasets, such as public repositories on GitHub. As you gain more confidence, you can combine these skills into shell scripts that automatically retrieve, process, and act upon real-time data. Don't worry if `jq`'s syntax feels unusual at first; with practice, you'll find it much more readable and intuitive than manually parsing raw JSON text.
