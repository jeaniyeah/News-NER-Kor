## Project 
The model trained with this code recognizes named entities in input sentences and predicts one label from a set of 150 labels for each named entity, thereby performing labeling for the input sentences.  
datasetLabeling: Code for labeling the 2022 Named Entity Analysis Corpus provided by the National Institute of Korean Language.   
train: Code for fine-tuning a base model using the provided dataset.   
base model: https://huggingface.co/xlm-roberta-large-finetuned-conll03-english  
dataset: https://huggingface.co/datasets/yeajinmin/NER-News-BIDataset   
For dataset details, please refer to the README in the provided link above.   

## Labeling Method
a. If '_' is a standalone token at the beginning, it is excluded and labeled. This is exemplified by excluding the 6th token in the provided code.  
b. If '_' is tokenized with a word, it is labeled regardless. 
  For Instance, '_무' in '_무/효/사/유' would be labeled together.  
c. If a particle(조사) is attached to a token and is tokenized with a word, it is labeled together.  
d. If one entity is incorrectly tokenized but should have the same label, it is labeled as "B-Entity", "I-Entity" continuation.   
  For example, if '설 명절' is tokenized as '설명 / 절', it would be labeled as "설명(B-DT_DAY)" / "절(I-DT_DAY)".   
e. If one entity is incorrectly tokenized and should have a different label, discard it. When coding for the creation of the training dataset, avoid labeling in such cases, even if the original data includes labels.   
   For instance, '청정보령' should only be labeled as '보령,' but if it is tokenized as '청/정보/령,' and '보령' cannot be labeled, it is discarded.   

## Why no IGN labels in Korean NER datasets?
If IGN labels were used in the training of the existing named entity recognition model, the tagging approach involves predicting the label only for the first token of the named entity.   
Subsequently, all following tokens are tagged with IGN labels. During training, if the predicted label is IGN, it is ignored, irrespective of the specific label it would have been predicted as.    
Additionally, using whitespace as a splitter, if the entity spans only one word, all tokens are recognized with the label of the first token.    
However, it is important to note that this approach might not be suitable for Korean.   
The reason for this is rooted in the fact that Korean named entities frequently include particle(조사).  
Therefore, if words are split based on whitespace, particles will consistently be attached, making the aforementioned approach inappropriate.  
When training with IGN labels in this manner, the model only labels the first token. In a sentence like '미국인과 처음 만났다,' where '미국인' should be predicted as 'CV_TRIBE,' if it is tokenized as '미국 / 인,' even if the model predicts correctly, only '미국' gets labeled as 'CV_TRIBE,' providing an inaccurate result.   
Adopting the conventional foreign language approach of labeling everything based on whitespace would also yield undesired results; for example, '미국인과' would be labeled as 'CV_TRIBE,' which is not the intended outcome.   
Additionally, even in cases where particles are omitted, the user's improper use of spacing can introduce variability. For example, if two named entities that should be predicted with the same label are written without spaces, such as '대구부산,' treating them as a single token based on whitespace would result in the recognition of both entities as one. This can lead to incorrect results being presented to the user.

