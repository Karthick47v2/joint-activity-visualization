# joint-activity-visualization

## Prerequisites

- Python (>= 3.7)
- scikit-image (>= 0.19.3)
- mediapipe (== 0.9.0)
- opencv (>= 4.1)

> You can directly install required python packages using,

```
    pip install -r requirements.txt
```

## Strategy

As of now, heatmap visualization is based only on difference between (coordinates difference) 2 consecutive frames. In order to calculate the difference **Manhattan distance** formula is used as it is computationally efficient than Euclidean distance (To improve efficiency in realtime). Currently, difference have to clipped to maximum of 1, you can set your own constraints under `calc_coord()` function at `coord_diff....` statement.

## Visualization

Mediapipe and OpenCV doesn't support multicolor in single line. So in order to visualize intensity of colors in each pixels of line, firstly line between to points have be partitioned multiple small lines using scikit-image and then used opencv to iterate through those points and draw those lines with different colors. This is the only bottleneck in this whole process as, drawing thousands of small lines will take considerable amount of time (looks like near-realtime).

## Parameter Tuning

Detailed explanation about tuning parameters / variables used in project file

- `line_size` = width of line that used to draw skeleton.
- `amplification_factor` = distance between joint coordinates would be small in consecutive frames are these are differences of normalized landmarks (x and y ranges from 0-1) so it is used to amplify difference.
- `max_b/g/r` = values of BGR joint aka high intensity point
- `b/g/r_spread` = how BGR spread when going out from joint (intensity will decrease)

## Loops in `draw_hm_skeleton()`

Outer loop will iterate over each joint-to-joint connection a.k.a bones, so it will have 2 joints at end of it. Firstly, the whole line (for example line from left wrist joint to left elbow joint) will partitioned into multiple small points of lines. Then 1st loop will iterate over those partitions and draw heatmap for one side of line (lets say wrist joint side) and then next loop will iterate over those partitions in reverse order and draw heatmap for other side of line (elbow joint).

## Real time / Post processing

> If you are going to process a video file, put file name in `cv2.VideoCapture(<input>)` or else put 0 (if webcam) 1,2... (if additional cam).
> If you **do not** need to record and output a video with skeleton, comment all lines related to variable `out`.
> Currently video saving format is set to, mp4 - 24fps, check opencv fourcc for other formats.

## Additional Notes

- Mediapipe predicts 33 keypoints (0-32) that why lists and loops are initialzed to that values.
- `mp_pose.POSE_CONNECTIONS`, is a config offered by mediapipe for connecting each joints.
