# ChatGLM_6b_ptuning
ChatGLM-6B自定义数据集微调
本项目由ChatGLM_6B和finetune_dataset_maker两个项目完成。
```shell
https://github.com/THUDM/ChatGLM-6B.git
```
```shell
https://github.com/huang1332/finetune_dataset_maker.git
```
先利用finetune_dataset_maker制作数据集，再将数据集放入AdvertiseGen中
之后就可以进行ptuning

## 模型效果

### 例子
#### 示例1
* Input: 将以下短文的开头补充完整。 近年来，人工智能发展迅速，给整个社会带来了很多变化。其中，________
* Label: 其中，人工智能在生产制造、医疗健康、金融、教育等领域的应用越来越广泛。
* Output[微调前]: 近年来，人工智能发展迅速，给整个社会带来了很多变化。其中，人工智能技术在医疗、金融、农业、工业等领域得到广泛应用，大大提高了生产效率和生活质量，但同时也引发了一些争议和担忧，比如对就业的影响、隐私保护等问题。
* Output[微调后]: 人工智能快速发展，给整个社会带来了很多变化。其中，人工智能的应用领域越来越广泛，从医疗保健到金融保险，从智能家居到无人驾驶汽车，都受到人们的青睐。

#### 示例2

* Input: 更正给定句子中的语法错误 It have been raining all day.
* Label: It has been raining all day.
* Output[微调前]: 更正后的句子为：It has been raining all day.
* Output[微调后]: It has been raining all day.

训练
```shell
python main.py --model_name_or_path ./chatglm-6b --output_dir ./output --do_train --train_file ./AdvertiseGen/train.json --validation_file ./AdvertiseGen/dev.json --prompt_column content --response_column summary --overwrite_cache --output_dir ./output/adgen-chatglm-6b-pt-1282e-2 --overwrite_output_dir --max_source_length 64 --max_target_length 64 --per_device_train_batch_size 1 --per_device_eval_batch_size 1 --gradient_accumulation_steps 16 --predict_with_generate --max_steps 3000 --logging_steps 10 --save_steps 1000 --learning_rate 2e-2 --pre_seq_len 128 --quantization_bit 4

```
测试
```shell
python web_demo.py --model_name_or_path THUDM/chatglm-6b --ptuning_checkpoint output\adgen-chatglm-6b-pt-1282e-2\checkpoint-3000 --pre_seq_len 128 --quantization_bit 8
```


## 总结
现实证明ptuning需要更好更专业的数据集，微调才能将微调的更好，不然只会破坏模型。