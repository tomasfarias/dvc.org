# Add Output to Stage

There are situations where we have executed a stage (either by writing
`dvc.yaml` manually and using `dvc repro`, or with `dvc run`), but later notice
that output(s) already existing in workspace are missing from `dvc.yaml`.
Outputs are files or directories written by the stage commands which we want to
track with DVC. Re-executing a stage to specify all the <abbr>outputs</abbr> is
an expensive step, as it requires time and resources. Follow the steps below to
add already existing files or directories as output(s) to a stage (`outs` field
in `dvc.yaml`) without executing it again.

We start with an example `prepare` stage in `dvc.yaml`, which has a single
output. To add a missing output `data/validate` to this stage, we can edit
`dvc.yaml` like this:

```git
 stages:
   prepare:
     cmd: python src/prepare.py
     deps:
     - src/prepare.py
     outs:
     - data/train
+    - data/validate
```

> Note that you can also use `dvc run` with the `-f` and `--no-exec` options to
> add another output to the stage:
>
> ```dvc
> $ dvc run -f --no-exec \
>           -n prepare \
>           -d src/prepare.py \
>           -o data/train \
>           -o data/validate \
>           python src/prepare.py
> ```
>
> The `-f` option overwrites the stage in `dvc.yaml`, and the `--no-exec` option
> updates the stage without executing it.

Finally, we need to run `dvc commit` to save the newly specified output(s) to
the <abbr>cache</abbr> (and to update the corresponding hash values in
`dvc.lock`):

```dvc
$ dvc commit
```