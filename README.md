## Triplet Loss for FaceRecognition

### Introduction  

Modified from [wjgaas/DeepID2](https://github.com/wjgaas/DeepID2), update the source code to fit the latest verison of [BVLC/caffe](https://github.com/BVLC/caffe).   

Details:   
1. add **pair_size** parameter for image_data_layer to shuffle training examples in each pair_size. Normally, pair_size = 2.  
   therefore, most of the every two training examples are of the same label. 
   **in my experiments，I only make positive pairs**.

    in the prototxt:  
```
layer {
  name: "data"
  type: "ImageData"
  top: "data"
  top: "label"
  include { 
    phase: TRAIN
  }
  transform_param {
    scale: 0.0078125
    mirror: true
  }
  image_data_param {
    source: "/media/wujiyang/data/FaceData/train.txt"
    batch_size: 256
    shuffle: true
    pair_size: 2
  }
}   
```  
2. add **normalization_layer**  for feature normalization

    normalization_layer.hpp
    normalization_layer.cpp
    normalization_layer.cu
     
3. add **identity2verify_layer** for blob split   
    in data_layer，the positive pairs are read in a batch, what this layer do is spliting them into two blobs.  
### Usage
    In the prototxt  
```
layer {
	name: "split-layer"
	type: "Identity2Verify"
	bottom: "fc5"
	bottom: "label"
	top: "feature1"
	top: "feature2"
	top: "sim"
} 
```    

**Attention:**   

When training models with the CASIA-WebFace dataset，trying to set margin = 0.1 or 0.2.   
In my experiments,  when margin = 1, the loss didn't converge at all. 