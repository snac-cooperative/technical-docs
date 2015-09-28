# SNAC Server Architecture

The system will be architected as a LAMP system, with the following components:

* Linux: CentOS 7
* Apache: Apache 2 web server
* PHP: PHP 7
* PostgreSQL: Postgres

Each component of the architecture will run on this platform.  Any sub-component must either produce it's own http server on an available port, such as Elastic Search, or utilize the main Apache web server running a virtual host.

The following diagrams describe the architecture of internal components:

* ![Overall Server Architecture](http://gitlab.iath.virginia.edu/snac/Documentation/raw/master/Specifications/Originals/SNAC%20Server%20Architecture.svg)
