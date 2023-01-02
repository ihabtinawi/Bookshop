# vim: set tabstop=8 softtabstop=8 noexpandtab:

install:
	@composer install --no-interaction --no-progress
	$(MAKE) init

init:
	$(MAKE) clear
	@rm -Rf web/media/*
	@rm -Rf app/logs/*.log
	$(MAKE) db
	@php bin/console doctrine:fixtures:load --append 
	$(MAKE) sonata

db:
	@php bin/console doctrine:database:drop --force --if-exists
	@php bin/console doctrine:database:create
	@php bin/console doctrine:schema:create

clear:
	@php bin/console cache:clear --no-warmup --no-debug
	@php bin/console cache:warmup --no-debug
	@php bin/console doctrine:cache:clear-metadata --quiet --no-debug
	@php bin/console doctrine:cache:clear-query --quiet --no-debug
	@php bin/console doctrine:cache:clear-result --quiet --no-debug

migrations:
	@php bin/console doctrine:migrations:migrate --no-interaction

sonata:
	@php bin/console sonata:page:update-core-routes --site=all
	@php bin/console sonata:page:create-snapshots --site=all

clean:
	@rm -rf web/media/*
	@php bin/console cache:clear --no-warmup
	@php bin/console cache:warmup
	@php bin/console assets:install --symlink

fix-code-style:
	@php bin/phpcbf --standard=PSR2 -n --extensions="php" src/

lint:
	@echo -e "\x1B[32mRunning PHPCS\x1B[0m"
	@php bin/phpcs --standard=PSR2 --extensions=php --encoding=UTF-8 --ignore="*vendor*" --tab-width=120 -p src/
	@echo -e "\x1B[32mLinting Twig files\x1B[0m"
	@php bin/console lint:twig src/
	@echo -e "\x1B[32mLinting YAML files\x1B[0m"
	@php bin/console lint:yaml src/

test: export SYMFONY_DEPRECATIONS_HELPER := weak
test: export SYMFONY_ENV := test
test: lint
	$(MAKE) init
	@php bin/behat
	@php bin/phpunit
