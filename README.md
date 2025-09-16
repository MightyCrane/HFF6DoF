# HFF6DoF — Official Implementation (Code Coming Soon)

This is the **official repository** for the paper **"6DoF Pose Estimation of Transparent Objects: Dataset and Method"**.  
**Code coming soon.**  
**Dataset coming soon.** 
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
Distribution summary
A) single_data — per-class counts
Class	train_data_list	visual_data	test_data_list	hard_train_data_list	hard_test_data_list	Total
cube_136	1310	11	232	1542		1542
cube_166	1349	12	239	1588		1588
cube_216	1314	12	233	1547		1547
cuboid_210	1353	12	239	1592		1592
cuboid_330	1306	11	231	1537		1537
goblet_180	1360	12	240	1600		1600
handle_cup_280	1357	12	240	1597		1597
handle_cup_440	1360	12	240	1600		1600
handle_cup_480	1360	12	240	1600		1600
icecream_cup_175	1287	12	228	1515		1515
juice_cup_250	1352	12	239	1591		1591
juice_cup_340	1357	12	240	1597		1597
juice_cup_350	1360	12	240	1600		1600
quartet_cup_80	1325	11	234	1559		1559
quartet_cup_90	1327	12	235	1562		1562
short_cup_220	1360	12	240	1600		1600
water_cup_270	1049	9	186	1235		1235
water_cup_300	1300	12	230	1530		1530
whisky_cup_225	1360	12	240	1600		1600
whisky_cup_270	1288	11	228	1516		1516

Column totals (single_data):
train_data_list = 26,434 · visual_data = 233 · test_data_list = 4,674 · hard_train_data_list = 52,747

B) multi_data — per-sequence counts
Seq ID	train_data_list	visual_data	test_data_list	hard_train_data_list	hard_test_data_list	Total
00				1,584		1,584
01				1,600		1,600
02					1,600	1,600
03				1,542		1,542
04				1,298		1,298
05				1,576		1,576
06				1,591		1,591
07				1,600		1,600
08					1,544	1,544
09					1,356	1,356
10					1,524	1,524
11					1,583	1,583
12				1,597		1,597
13				1,538		1,538
14				1,517		1,517
15					1,532	1,532
16				1,596		1,596
17				1,600		1,600
18				1,584		1,584
19				1,416		1,416

Column totals (multi_data):
hard_train_data_list = 52,747 · hard_test_data_list = 9,139
(These match your provided sums; other columns are not used for multi_data.)




---
If you use our dataset or code, please consider citing the paper **"6DoF Pose Estimation of Transparent Objects: Dataset and Method"**.


