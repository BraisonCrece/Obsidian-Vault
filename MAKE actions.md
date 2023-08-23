- `install` -> Initial project setup. Build containers, install dependencies, compile assets, prepare and seed databases.
- `uninstall ` -> Remove all docker images used by any service and named volumes. NOTE: This will also kill all databases and you need to rerun `make install`.
- `db.migrate` -> Migrate database.
- `db.reset` -> Delete Postgres data volume, setup dev & test database, seed dev database.
- `db.session` -> Run `psql` in newly created `database` container.
- `rails.console` -> Open Rails console in newly created `web` container.
- `rails.console.test` -> Open Rails console in newly created `test` container.
- `rails.console.exec` -> Open Rails console in existing `web` container.
- `rails.console.exec.test` -> Open Rails console in existing `test` container.
- `serve.api-docs` -> Serve API docs via ReDoc.
- `test` -> Run tests in newly created container with optional arguments `args`. If `args` is supplied it is handed over to `rspec` inside the container. Example usage: `make test args=spec/models/client_spec.rb`.
- `test.exec` -> Run tests in running test container with optional arguments `args` (see also `make test`).
- `test.session` -> Run bash in newly created test container.
- `release` -> Trigger new release with optional `name` argument. Example usage: `make release name=r71`.
- `yarn.build` -> Run `yarn build` in webpack container.
- `yarn.jest.t` -> Run `yarn jest -t` (--testNamePattern) in webpack container with required argument `pattern`.
- `yarn.tc` -> Run `yarn run tc` in webpack container.
- `yarn.test` -> Run `yarn test` in webpack container.
- `rubocop.autocorrect` -> Run rubocop's autocorrect in web container
- `rubocop` -> Run rubocop in web container



```bash
killport() { 
	pid=$(sudo lsof -t -i:$1) 
	if [ -z "$pid" ]; 
	then 
		echo "No process found on port $1" 
	else 
		echo "Killing process on port $1 with pid $pid" 
		sudo kill -9 $pid 
	fi 
}
```


