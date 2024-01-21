# Hugging Face PEFT Library

Hugging Face PEFT allows you to fine-tune a model without having to fine-tune all of its parameters.

Training a model using Hugging Face PEFT requires two additional steps beyond traditional fine-tuning:

* Creating a PEFT config
Converting the model into a PEFT model using the PEFT config
Inference using a PEFT model is almost identical to inference using a non-PEFT model. The only difference is that it must be loaded as a PEFT model.

* Converting a Transformers Model into a PEFT Model

* Training with a PEFT Model

* Checking Trainable Parameters of a PEFT Model

* Saving a Trained PEFT Model

* Generating Text from a PEFT Model
