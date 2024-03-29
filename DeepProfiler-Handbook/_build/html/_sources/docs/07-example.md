# 7. Run example data

The following instructions help first-time users to download some example data and run important DeepProfiler commands.



1. `cd /home/ubuntu`
2. `wget https://imaging-platform.s3.amazonaws.com/projects/deepprofiler-examples/example-data.tar.gz`
3. `tar -xzf example-data.tar.gz`
4. Your folder `example-data` is now your project directory for this example project. In other words, when running commands in the DP folder, you refer to this as root.
5. `cd /home/ubuntu/DeepProfiler `
6. If you are running DP on a docker, mount docker via ``docker run -it --rm -v ~/home/ubuntu/example_data:/example_data michaelbornholdt/deep_profiler:0.3.0``
7. Check that the mount worked: ``ls /example_data/` should give inputs and outputs`
8. Prepare images via: ``python3 deepprofiler --root=/example_data/ --config=config.json prepare``
9. in ``/jamboree/example_data/inputs/config/config.json`` change the `profile: checkpoint` to ``"None"``
10. ``python3 deepprofiler --root=/example_data/ --config=config.json profile``
11. Aggregate data via
    1. `cd /example_data/`
    2. `Python3 run_aggregation.py`
12. Plot correlation matrix (optional)
