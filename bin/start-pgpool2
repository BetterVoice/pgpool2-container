#!/usr/bin/python

from jinja2 import Environment, FileSystemLoader
from subprocess import check_call

import hashlib
import os

def pgpool_get_configuration():
	configuration = { }
	# Get the port pcp should listen on.
	value = os.getenv('PCP_PORT', 9898)
	configuration.update({ 'pcp_port': value })
	# Get the PCP user.
	value = os.getenv('PCP_USER', 'postgres')
	configuration.update({ 'pcp_user': value })
	# Get the PCP user password.
	value = os.getenv('PCP_USER_PASSWORD', 'bettervoice')
	hash = hashlib.md5()
	hash.update(value)
	configuration.update({ 'pcp_user_password': hash.hexdigest() })
	# Get the port pgpool should listen on.
	value = os.getenv('PGPOOL_PORT', 5432)
	configuration.update({ 'pgpool_port': value })
	# Get the configuration for the backends.
	# FORMAT - INDEX:HOST:PORT
	value = os.getenv('PGPOOL_BACKENDS', '1:localhost:5432').split(',')
	for item in value:
		if not len(item.split(':')) == 3:
			raise ValueError('Invalid Backend: %s' % item)
	configuration.update({ 'pgpool_backends': value })
	return configuration

def run(app, *args):
	check_call([app] + list(args))

def write(template, path):
	with open(path, "wb") as output:
		output.write(template)

if __name__ == "__main__":
	# Initialize Jinja2
	loader = FileSystemLoader('/usr/share/pgpool2')
	templates = Environment(loader = loader)
	# Load the configuration into a dictionary.
	configuration = pgpool_get_configuration()
	# Write PCP user credentials.
	pcp = templates.get_template('pcp.conf.template') \
								 .render(configuration)
	write(pcp, '/etc/pgpool2/pcp.conf')
	pgpool = templates.get_template('pgpool.conf.template') \
										.render(configuration)
	write(pgpool, '/etc/pgpool2/pgpool.conf')
	# Start the container.
	try:
		run('pgpool', '-n', '-f', '/etc/pgpool2/pgpool.conf', '-F', '/etc/pgpool2/pcp.conf')
	except KeyboardInterrupt:
		print 'Exiting...'