## 常见问题

**Q1. TensorFlow模型转换过程中，提示『Unknown shape for input tensor[tensor name: "input"]， Please define shape of input here』？**  
A：该提示信息表示无法从TensorFlow的pb模型中获取到输入tensor(tensor名为"input:)的shape信息，所以需要用户手动在提示后输入详细的shape信息，如None,224,224,3 其中None表示Batch


**Q2. TensorFlow模型转换失败怎么解决?**  
A: 如果并非是由缺少OP导致，那可能是由于TensorFlow模型转换时（NHWC->NCHW格式转换导致），在这种情况下，采用如下方式进行转换，同时固化输入大小的方式，继续尝试转换，见如下命令，转换过程中，根据提示，输入相应tensor的固化shape大小
```
x2paddle -f tensorflow -m tf.pb -s pd-model --without_data_format_optimization --define_input_shape
```

> 1. 目前Tensorflow的CV模型大部分均为`NHWC`的输入格式，而Paddle的默认输入格式为`NCHW`，因此X2Paddle在转换过程中，会对如`axis`， `shape`等参数进行转换，适应Paddle的NCHW格式。但在这种情况下，可能会由于TensorFlow模型太复杂，导致出错。  指定`--without_data_format_optimization`后，会停止对`axis`，`shape`等参数的优化（这可能会带来一定数量的transpose操作）

**Q3. ONNX模型转换过程中，提示『Unknown shape for input tensor[tensor name: "input"] -> shape: ['batch', 'sequence']， Please define shape of input here』**  
A：该提示信息表示从ONNX的模型中获取到输入tensor(tensor名为"input:)的shape是语义象征性的['batch', 'sequence']，而不是dim为int类型的shape，从而可能会因为部分node的shape无法推理，导致转换失败。所以用户可以尝试手动在提示后输入详细的shape信息，如:-1,3,224,224  其中-1表示Batch
