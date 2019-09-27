# mcf-tracker
This repository includes modified code of [original mcf-tracker](https://github.com/nwojke/mcf-tracker) and it can utilize the primitive action features for human tracking.


## Cloning this repository and building a docker image
```
git clone --recursive https://github.com/hitottiez/mcf-tracker.git
cd mcf-tracker
docker build -t <tagname> .
```

## Running a docker container and login
Run:
```
docker run -d -it --name <container_name> \
    --mount type=bind,src=/<path/to/mcf-tracker>/,dst=/opt/multi_actrecog/mcf-tracker \
    --mount type=bind,src=/<path/to/dataset>/,dst=/mnt/dataset \
    <image name> /bin/bash
```

Login:
```
docker exec -it <container_name> /bin/bash
```

Make:
```
make
```

## Training
Convert Okutama-Action dataset to split images using ffmpeg and put feature files (`det.txt`, `cnn.txt`, `{rgb, flow, fusion}_.txt`) in the proper directory (refer [mht-paf](https://github.com/hitottiez/mht-paf)).

Example in case that dataset is `/mnt/dataset/okutama_action_dataset/okutama_3840_2160/` and the file of primitive action feature is `rgb_tsn.txt`:

```
python okutama_trainer.py \
        --data_root /mnt/dataset/okutama_3840_2160/ \
        --tsn_modality rgb \
        --model_prefix rgb
```
When not using the primitive action feature, set `--tsn_modality` as `none`.
(as same as original mcf-tracker)

Then, `rgb_observation_cost_model.pkl` and `rgb_transition_cost_model.pkl` are created in the current directory.

## Running human tracking and action recognition
Run:
```
python okutama_tracking_app.py \
    --data_root /mnt/dataset/okutama_action_dataset/okutama_3840_2160/ \
    --save_root /mnt/dataset/okutama_action_dataset/mcf-tracker_tracking_result \
    --tsn_modality rgb \
    --observation_cost_model rgb_observation_cost_model.pkl \
    --transition_cost_model	rgb_transition_cost_model.pkl \
    --observation_cost_bias -3 \
    --make_movie
```
If `--tsn_modality` is set as `none`, `--tsn_modality` is set as `rgb`.
`--tsn_modality` is ignored in the code.

Then, `contextlog.dat` is created in `/mnt/dataset/okutama_action_dataset/mcf-tracker_tracking_result` as the following directory structure:

```
/mnt/dataset/okutama_action_dataset/test_result/
├── 1.1.8
│   └── contextlog.dat
├── 1.1.9
├── 1.2.1
├── 1.2.10
├── 1.2.3
├── 2.1.8
├── 2.1.9
├── 2.2.1
├── 2.2.10
└── 2.2.3
```

## Evaluation
Refer [deepsort](https://github.com/hitottiez/deepsort).
