# HFF6DoF — Official Implementation (Code Coming Soon)

This is the **official repository** for the paper **"6DoF Pose Estimation of Transparent Objects: Dataset and Method"**.  
**Dataset link: https://pan.baidu.com/s/18P54omUkwel9j3hdOuZ0PQ?pwd=i8kr**  
**Paper link: https://www.mdpi.com/1424-8220/26/3/898**
---

# TDoF20 Dataset — Directory & File Reference (English)



## Tree (concise)

```
TDoF20\
├── dataset_config/
│   ├── classes.txt
│   ├── intrinsics.json
│   └── {train,test,hard_train,hard_test,visual}_data_list.txt
├── models/
├── diameters/
├── farthest/
├── single_data/
│   ├── <object>/{rgb,depth,broken_depth,mask,labels,pose}/
│   └── …
├── multi_data/
│   ├── 00/{rgb,depth,broken_depth,mask,labels,pose}/
│   └── 01–19/{…}
└── single_data_bp/
    ├── <object>/{rgb,depth,broken_depth,mask,pose}/
    └── {intrinsics.json, *.ply, diameter.txt, farthest.txt, train.txt, test.txt, train.json, test.json, …}
```

---

## Top level & configuration

### `dataset_config/`

* **Purpose**: global metadata & split indices.
* **Contains**

  * `classes.txt` — ordered class list.
  * `intrinsics.json` — camera intrinsics.
  * `train_data_list.txt` — **single\_data** training index; complementary with `test_data_list.txt`.
  * `test_data_list.txt` — **single\_data** test index; `visual_data.txt` is sampled from here for demos.
  * `hard_train_data_list.txt` — **all single\_data** + **most multi\_data** (excluding sequences `{02,08,09,10,11,15}`).
  * `hard_test_data_list.txt` — multi-data **hard** subset: sequences `{02,08,09,10,11,15}`.

  
* **Notes**

  * Typical image size is **480×640** unless otherwise stated.

### `models/`

* **Purpose**: CAD meshes per class.
* **Contains**: one `*.ply` per object class.


### `diameters/`

* **Purpose**: object scale per class.
* **Contains**: one text file per class with the object **diameter**.


### `farthest/`

* **Purpose**: geometric keypoints per class.
* **Contains**: one text file per class with **8 farthest-point sampled (FPS) 3D keypoints**.


---

## `single_data/` (single-object scenes)

* **Purpose**: per-class captures where each frame contains a **single** transparent object.
* **Structure**

  ```
  single_data/
  ├── <object>/
  │   ├── rgb/            # *.jpg, color images
  │   ├── broken_depth/   # *.png, 16-bit raw depth with holes
  │   ├── depth/          # *.png, stabilized depth
  │   ├── mask/           # *.png, integer labels image
  │   ├── labels/         # *.txt, lightweight per-frame meta
  │   └── pose/           # *.pkl, GT poses & per-frame meta
  └── … (other 19 classes, same layout)
  ```

---

## `multi_data/` (multi-object scenes)

* **Purpose**: scene sequences with **3–5** transparent objects per frame (sequence IDs `00–19`).
* **Structure**

  ```
  multi_data/
  ├── 00/
  │   ├── rgb/            # *.jpg
  │   ├── broken_depth/   # *.png, 16-bit raw depth with holes
  │   ├── depth/          # *.png, stabilized depth
  │   ├── mask/           # *.png, integer labels image
  │   ├── labels/         # *.txt
  │   └── pose/           # *.pkl, GT poses for all instances in the frame
  └── 01–19/ (same layout)
  ```

---

## `single_data_bp/` (benchmark packs)

* **Purpose**: **uniform, self-contained** per-class packs for fair comparison and easy repro.
* **Structure**

  ```
  single_data_bp/
  ├── <object>/
  │   ├── rgb/            # *.jpg
  │   ├── broken_depth/   # *.png, raw depth with holes
  │   ├── depth/          # *.png, stabilized depth (default depth used in COCO exports)
  │   ├── mask/           # *.png, integer labels image
  │   ├── pose/           # *.pkl
  │   └── {intrinsics.json, *.ply, diameter.txt, farthest.txt, train.txt, test.txt}
  └── {train.json, test.json, train_new.json, test_new.json (if present)}
  ```

---
###############################################################
Got it — here’s a clean, **English README-style** dataset intro.
(I’ve renamed “Top level & configuration” → **“Root-level metadata & splits”**, and made **`models/`, `diameters/`, `farthest/`, `single_data/`, `multi_data/`, `single_data_bp/`** all follow the **same “Role / Contents / Notes”** structure. I’ve also removed the hard “3–5” claim from the main text; see the note under `multi_data/`.)

# TDoF20 — Directory & File Reference

> **Modalities (terminology)**
>
> * **`broken_depth/`** — raw 16-bit depth with holes/wrong depths (0 = invalid).
> * **`depth/`** — stabilized/hole-filled depth aligned to the same frame.
> * **`mask/`** — integer semantic mask (pixel value = class ID; 0 = background).
> * **`pose/`** — per-frame 6DoF ground truth & metadata (`*.pkl`).

## Tree (concise)

```
TDoF20\
├── dataset_config/
│   ├── classes.txt
│   ├── intrinsics.json
│   └── {train,test,hard_train,hard_test,visual}_data_list.txt
├── models/
├── diameters/
├── farthest/
├── single_data/
│   ├── <object>/{rgb,depth,broken_depth,mask,labels,pose}/
│   └── …
├── multi_data/
│   ├── 00/{rgb,depth,broken_depth,mask,labels,pose}/
│   └── 01–19/{…}
└── single_data_bp/
    ├── <object>/{rgb,depth,broken_depth,mask,pose}/
    └── {intrinsics.json, *.ply, diameter.txt, farthest.txt, train.txt, test.txt, train.json, test.json, …}
```

---

## Root-level metadata & splits — `dataset_config/`

* **Role**: global metadata and split indices.
* **Contents**:

  * `classes.txt` — ordered class list (defines class IDs used in masks/poses).
  * `intrinsics.json` — camera intrinsics (incl. `depth_scale`).
  * `train_data_list.txt`, `test_data_list.txt` — **single\_data** splits (complementary).
  * `hard_train_data_list.txt`, `hard_test_data_list.txt` — “hard” setting: all **single\_data** + most **multi\_data** for train; the six hard **multi\_data** sequences `{02,08,09,10,11,15}` for test.
  * `visual_data.txt` — small visualization subset (sampled from the regular test list).
* **Notes**: every line indexes a frame stem; all modalities for that stem exist in the corresponding subfolders.

## CAD meshes — `models/`

* **Role**: per-class meshes for projection/alignment/evaluation.
* **Contents**: one `*.ply` per class.
* **Notes**: mesh scale matches the provided diameters.

## Object diameters — `diameters/`

* **Role**: per-class scale values (used e.g. in ADD/ADD-S normalization).
* **Contents**: one text file per class (`*_diameter.txt`).
* **Notes**: single scalar per class.

## Farthest keypoints — `farthest/`

* **Role**: per-class 3D keypoints for training.
* **Contents**: one text file per class (`*_farthest.txt`) with **8 FPS keypoints**.
* **Notes**: used together with the object **center** to form **9 total 3D keypoints**.

## Single-object scenes — `single_data/`

* **Role**: per-class captures; one transparent object per frame.
* **Contents**:

  * `rgb/` — color images (`*.jpg`)
  * `broken_depth/` — raw depth (`*.png`, 16-bit; holes/wrong depths; 0 = invalid)
  * `depth/` — stabilized/hole-filled depth (`*.png`)
  * `mask/` — integer labels image (`*.png`, pixel = class ID; 0 = background)
  * `labels/` — auxiliary per-frame text (lightweight meta)
  * `pose/` — per-frame GT (`*.pkl`, e.g., `poses`, `cls_ids`, `center_3ds`)
* **Notes**: all folders share the same frame stem; other 19 classes follow the same layout.

## Multi-object scenes — `multi_data/`

* **Role**: scene sequences (`00–19`) containing **multiple** transparent objects per frame.
* **Contents** (per sequence):

  * `rgb/`, `broken_depth/`, `depth/`, `mask/`, `labels/`, `pose/` — same semantics as in `single_data/`.
* **Notes**:

  * Masks may include **several class IDs** per frame.
  * The commonly used “hard” test subset is `{02,08,09,10,11,15}` (as defined in `dataset_config`).
  * Your original data-collection note mentions “3–5 objects at a time”; actual counts can vary by frame/sequence.

## Benchmark packs (uniform, self-contained) — `single_data_bp/`

* **Role**: standardized per-class packs for fair comparison and easy reproduction.
* **Contents**:

  * Per class: `rgb/`, `broken_depth/`, `depth/`, `mask/`, `pose/`
  * Per class meta: `{intrinsics.json, *.ply, diameter.txt, farthest.txt, train.txt, test.txt}`
  * Dataset-level COCO exports: `{train.json, test.json, …}` (if provided)
* **Notes**:

  * **Unified frame count**: each class has **2,400 frames by construction** (resampled/standardized; not the raw `single_data/` count).
  * COCO exports default to **`depth/`** for depth paths.

---


## Distribution summary

## Regular split (single\_data only)

| Class              | train\_data\_list | visual\_data | test\_data\_list |      Total |
| ------------------ | ----------------: | -----------: | ---------------: | ---------: |
| cube\_136          |              1310 |           11 |              232 |       1542 |
| cube\_166          |              1349 |           12 |              239 |       1588 |
| cube\_216          |              1314 |           12 |              233 |       1547 |
| cuboid\_210        |              1353 |           12 |              239 |       1592 |
| cuboid\_330        |              1306 |           11 |              231 |       1537 |
| goblet\_180        |              1360 |           12 |              240 |       1600 |
| handle\_cup\_280   |              1357 |           12 |              240 |       1597 |
| handle\_cup\_440   |              1360 |           12 |              240 |       1600 |
| handle\_cup\_480   |              1360 |           12 |              240 |       1600 |
| icecream\_cup\_175 |              1287 |           12 |              228 |       1515 |
| juice\_cup\_250    |              1352 |           12 |              239 |       1591 |
| juice\_cup\_340    |              1357 |           12 |              240 |       1597 |
| juice\_cup\_350    |              1360 |           12 |              240 |       1600 |
| quartet\_cup\_80   |              1325 |           11 |              234 |       1559 |
| quartet\_cup\_90   |              1327 |           12 |              235 |       1562 |
| short\_cup\_220    |              1360 |           12 |              240 |       1600 |
| water\_cup\_270    |              1049 |            9 |              186 |       1235 |
| water\_cup\_300    |              1300 |           12 |              230 |       1530 |
| whisky\_cup\_225   |              1360 |           12 |              240 |       1600 |
| whisky\_cup\_270   |              1288 |           11 |              228 |       1516 |
| **Total**          |        **26,434** |      **233** |        **4,674** | **31,108** |

---

## Hard split (single\_data + multi\_data)

| ID                 |  Type  | hard\_train\_data\_list | hard\_test\_data\_list |      Total |
| ------------------ | :----: | ----------------------: | ---------------------: | ---------: |
| cube\_136          | single |                    1542 |                      — |       1542 |
| cube\_166          | single |                    1588 |                      — |       1588 |
| cube\_216          | single |                    1547 |                      — |       1547 |
| cuboid\_210        | single |                    1592 |                      — |       1592 |
| cuboid\_330        | single |                    1537 |                      — |       1537 |
| goblet\_180        | single |                    1600 |                      — |       1600 |
| handle\_cup\_280   | single |                    1597 |                      — |       1597 |
| handle\_cup\_440   | single |                    1600 |                      — |       1600 |
| handle\_cup\_480   | single |                    1600 |                      — |       1600 |
| icecream\_cup\_175 | single |                    1515 |                      — |       1515 |
| juice\_cup\_250    | single |                    1591 |                      — |       1591 |
| juice\_cup\_340    | single |                    1597 |                      — |       1597 |
| juice\_cup\_350    | single |                    1600 |                      — |       1600 |
| quartet\_cup\_80   | single |                    1559 |                      — |       1559 |
| quartet\_cup\_90   | single |                    1562 |                      — |       1562 |
| short\_cup\_220    | single |                    1600 |                      — |       1600 |
| water\_cup\_270    | single |                    1235 |                      — |       1235 |
| water\_cup\_300    | single |                    1530 |                      — |       1530 |
| whisky\_cup\_225   | single |                    1600 |                      — |       1600 |
| whisky\_cup\_270   | single |                    1516 |                      — |       1516 |
| 00                 |  multi |                    1584 |                      — |       1584 |
| 01                 |  multi |                    1600 |                      — |       1600 |
| 02                 |  multi |                       — |                   1600 |       1600 |
| 03                 |  multi |                    1542 |                      — |       1542 |
| 04                 |  multi |                    1298 |                      — |       1298 |
| 05                 |  multi |                    1576 |                      — |       1576 |
| 06                 |  multi |                    1591 |                      — |       1591 |
| 07                 |  multi |                    1600 |                      — |       1600 |
| 08                 |  multi |                       — |                   1544 |       1544 |
| 09                 |  multi |                       — |                   1356 |       1356 |
| 10                 |  multi |                       — |                   1524 |       1524 |
| 11                 |  multi |                       — |                   1583 |       1583 |
| 12                 |  multi |                    1597 |                      — |       1597 |
| 13                 |  multi |                    1538 |                      — |       1538 |
| 14                 |  multi |                    1517 |                      — |       1517 |
| 15                 |  multi |                       — |                   1532 |       1532 |
| 16                 |  multi |                    1596 |                      — |       1596 |
| 17                 |  multi |                    1600 |                      — |       1600 |
| 18                 |  multi |                    1584 |                      — |       1584 |
| 19                 |  multi |                    1416 |                      — |       1416 |
| **Total**          |        |              **52,747** |              **9,139** | **61,886** |




---
If you use our dataset, please consider citing the paper **"6DoF Pose Estimation of Transparent Objects: Dataset and Method"**.
```
@Article{s26030898,
    AUTHOR = {Wang, Yunhe and Wu, Ting and Zou, Qin},
    TITLE = {6DoF Pose Estimation of Transparent Objects: Dataset and Method},
    JOURNAL = {Sensors},
    VOLUME = {26},
    YEAR = {2026},
    NUMBER = {3},
    ARTICLE-NUMBER = {898},
    URL = {https://www.mdpi.com/1424-8220/26/3/898},
    ISSN = {1424-8220},
    DOI = {10.3390/s26030898}
}
```
