# Publishing on distribution channels

This recipe will walk you through a simple example that uses distribution channel to make releases available only to a subset of users in order to collect feedbacks before distributing the release to all users.

This example uses the **semantic-release** default configuration:
- [branches](../usage/configuration.md#branches): `['+([1-9])?(.{+([1-9]),x}).x', 'master', 'next', 'next-major', {name: 'beta', prerelease: true}, {name: 'alpha', prerelease: true}]`
- [plugins](../usage/configuration.md#plugins): `['@semantic-release/commit-analyzer', '@semantic-release/release-notes-generator', '@semantic-release/npm', '@semantic-release/github']`

## Initial release

We'll start by making the first commit of the project, with the code for the initial release and the message `feat: initial commit`. When pushing that commit on `master` **semantic-release** will release the version `1.0.0` and make it available on the default distribution channel which is the dist-tag `@latest` for npm.

The Git history of the repository is:

```bash
* feat: initial commit # => v1.0.0 on @latest
```

## Releasing a bug fix

We can now continue to commit changes release updates to our users. For example we can commit a bug fix with the message `fix: a fix`. When pushing that commit on `master` **semantic-release** will release the version `1.0.1` on the dist-tag `@latest`.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
```

## Releasing a feature on next

We now want to develop an important feature, which is a breaking change. Considering the scope of this feature we want to make it avaialbe at first only to our most dedicated users in order to get feedback. Once we get that feedback we can make improvements and ultimately make the new feature available to all users.

To implement that workflow we can create the branch `next` and commit our feature. When pushing that commit on `next` **semantic-release** will release the version `2.0.0` on the dist-tag `@next`. That means only the users installing our module with `npm install example-module@next` will receive the version `2.0.0`. Other users installing with `npm install example-module` will still receive the version `1.0.1`.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
| \
|  * feat: a very big feature \n\n BREAKING CHANGE: it breaks something # => v2.0.0 on @next
```

## Releasing a bug fix on next

One of dedicated user, starts using the new `2.0.0` release and reports a bug. We develop a bug fix and commit it with the message `fix: fix something on the big new feature`. When pushing that commit on `next` **semantic-release** will release the version `2.0.1` on the dist-tag `@next`.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
| \
|  * feat: a very big feature \n\n BREAKING CHANGE: it breaks something # => v2.0.0 on @next
|  * fix: fix something on the big new feature # => v2.0.1 on @next
```

## Releasing a feature on latest

We now want to develop a smaller, non-breaking feature. Its scope small enough that we don't need to have a phase of feedback and we can release it to all users right away.

If we were to push that feature on `next` only a subset of users would get it, and would need to wai for the end of our feedback period in order to make both the big and the small available to all our users. So instead we develop that small feature and commit it with the message `feat: a small feature` that we push to `master`. **semantic-release** will release the version `1.1.0` on the dist-tag `@latest` so all users can benefit from it right away.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
| \
|  * feat: a very big feature \n\n BREAKING CHANGE: it breaks something # => v2.0.0 on @next
|  * fix: fix something on the big new feature # => v2.0.1 on @next
*  | feat: a small feature # => v1.1.0 on @latest
```

## Porting a feature to next

Most of our users have now access to the small feature, but we still need to make it available to our users using the `@next` dist-tag. To do so we need to port our changes made on `master` (the commit `feat: a small feature`) into `next`. As `master` and `next` branches have diverged, this merge might require to resolve conflicts.
Once the conflicts are resolved and the merge commit is pushed to `next` **semantic-release** will release the version `2.1.0` on the dist-tag `@next` which contains both our small and big feature.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
| \
|  * feat: a very big feature \n\n BREAKING CHANGE: it breaks something # => v2.0.0 on @next
|  * fix: fix something on the big new feature # => v2.0.1 on @next
*  | feat: a small feature # => v1.1.0 on @latest
|  * feat: a small feature # => v2.1.0 on @next
```

## Adding a version to latest

After a period of feedback from our users using the `@next` dist-tag we feel confident to make our big feature availabled to all users. To do so we merge the `next` branch into `master`. There should be no conflict as `next` is strictly ahead of `master`. Once the merge commit is pushed to `next` **semantic-release** will add the version `2.1.0` to the dist-tag `@latest` so all users will receive it when installing out module with `npm install example-module`.

The Git history of the repository is now:

```bash
* feat: initial commit # => v1.0.0 on @latest
* fix: a fix # => v1.0.1 on @latest
| \
|  * feat: a very big feature \n\n BREAKING CHANGE: it breaks something # => v2.0.0 on @next
|  * fix: fix something on the big new feature # => v2.0.1 on @next
*  | feat: a small feature # => v1.1.0 on @latest
|  * feat: a small feature # => v2.1.0 on @next
|  * Merge master into next
| /
* Merge next into master # => v2.1.0 on @latest
```

We can now continue to push new fixes and features on `master`, or a new breaking change on `next` as we did before.
