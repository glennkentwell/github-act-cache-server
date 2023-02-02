# github-act-cache-server

Spin up a local Github artifact cache server to be used with [act](https://github.com/nektos/act) Github actions that uses [actions/cache](https://github.com/actions/cache)

## Run

```
ACT_CACHE_AUTH_KEY=foo docker compose up --build
```

## Act config
Ensure you add the following configuration to your `~/.actrc` file:
````
--env ACTIONS_CACHE_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_TOKEN=foo
````

## Observations
- You can set `ACT_CACHE_AUTH_KEY` and `ACTIONS_RUNTIME_TOKEN` to the value you want, but they must be the same
- The cache is persisted in Docker's named volumes(when using `docker-compose`) so it will survive between containers
- To purge the cache use the endpoint `/_apis/artifactcache/clean`. ie
````
curl -X POST -H 'Authorization: Bearer foo' 'http://localhost:8080/_apis/artifactcache/clean'
````

## Caveats
- The caching is global, meaning that it's shared across git projects and branches. As the container lacks the information of the Github context the action is running on it does not have access to `GITHUB_REPOSITORY`, `GITHUB_REF` or `GITHUB_BASE_REF` so it can do a better job restoring fallback caches or switching branches

## Acknowledgments

- This project started off the awesome https://github.com/anthonykawa/artifact-server and https://github.com/JEFuller/artifact-server (with docker support). 
