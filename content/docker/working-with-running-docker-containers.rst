======================================
Working with running docker containers
======================================

Attach to a running docker container and display standard out

.. code:: bash

    $ docker attach --sig-proxy=false de09edc60d19

To detach use `CTRL+c`.

A similar command is `docker logs` which will display the logs of a container
which is the standard output. `docker logs` will display the output of a
container from since it started.

.. code:: bash

    $ docker logs 5f606839d7c3

You can also follow the log in real time

.. code:: bash

    $ docker logs --follow 5f606839d7c3

Add `--timestamps` to display timestamps 

.. code:: bash

    $ docker logs --timestamps --follow 5f606839d7c3

`--since` will show logs from a given timestamp. For example, since 2017-01-01.

.. code:: bash

    $ docker logs --since 2017-01-01 5f606839d7c3


Display the metadata for a running container.

.. code:: bash

    $ docker inspect a51a4e8c0f78

Log into a running docker container

.. code:: bash

    $ docker exec --interactive --tty 1d7db16bb23b /bin/sh

To log out simply type `exit` or `CTRL+d`.


