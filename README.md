# github-act-cache-server

Spin up a local Github artifact cache server to be used with [act](https://github.com/nektos/act) Github actions that uses [actions/cache](https://github.com/actions/cache)

## Run

``` bash
# export env
export ACT_CACHE_AUTH_KEY=foo
# build image and start a container(listen 8080 port) 
docker compose up --build
```

## Act config
We need to let act to know the Cache Server URL and use which token. There are two ways to do so (choose one).

1. Ensure you add the following configuration to your `~/.actrc` file:
```
--env ACTIONS_CACHE_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_TOKEN=foo

# just run act
act {event}
```

2. Run act with env file (for example, put the file in project root) 
```
# ./act/cache.env
ACTIONS_CACHE_URL=http://localhost:8080/
ACTIONS_RUNTIME_URL=http://localhost:8080/
ACTIONS_RUNTIME_TOKEN=foo

# run act with .env file
act {event} --env-file act/cache.env
```
## Operations
- You can set `ACT_CACHE_AUTH_KEY` and `ACTIONS_RUNTIME_TOKEN` to the value you want, but they must be the same
- The cache is persisted in Docker's named volumes(when using `docker-compose`) so it will survive between containers
- To purge the cache use the endpoint `/_apis/artifactcache/clean`. ie
```
curl -X POST -H 'Authorization: Bearer foo' 'http://localhost:8080/_apis/artifactcache/clean'
```

## Caveats
- The caching is global, meaning that it's shared across git projects and branches. As the container lacks the information of the Github context the action is running on it does not have access to `GITHUB_REPOSITORY`, `GITHUB_REF` or `GITHUB_BASE_REF` so it can do a better job restoring fallback caches or switching branches

## Acknowledgments

- This project started off the awesome https://github.com/anthonykawa/artifact-server and https://github.com/JEFuller/artifact-server (with docker support). 
