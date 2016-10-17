
# pip

    $ pip3 uninstall python-highcharts
    Uninstalling python-highcharts-0.2.0:
      /usr/local/lib/python3.5/site-packages/highcharts/__init__.py

# pip with file

    $ cat requirements-dev.txt
    -r requirements-ci.txt
    ipdb==0.10.1
    pytest-sugar==0.7.1
    ipython==5.1.0
    aiodns==1.1.1
    $ pip3 install -r requirements-dev.txt
