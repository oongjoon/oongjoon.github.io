---
title : "Huggingface:TrainerCallback"



excerpt: "Huggingface TrainerCallback"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ,Callback]
# classes : wide
toc: true
toc_sticky: true
---
## TrainerCallback

In Huggingface, a class called Trainer makes training a model very easy. However, since the logging method is fixed, I came across a TrainerCallback while looking for a way to do different logging depending on the situation. Huggingface provides a class called TrainerCallback. By subclassing the TrainerCallback class, various Callback Classes are provided. Since various callback methods can be overridden through subclassing, I think that if you recognize the concept of inheritance, you can make it as if from scratch. The code in this article is taken from huggingface's docs. It is viewed from the perspective of an extremely rudimentary Python Coder, so I think that intermediate or advanced users may be puzzled. Thank you for taking a good look. (The detailed definition will be discussed in more detail later.)

## how the callback is implemented

Let's see how TrainerCallback works in Huggingface. The parent class called TrainerCallback is implemented by subclassing several other callback classes.

```python
class DefaultFlowCallback(TrainerCallback):
    """
    A :class:`~transformers.TrainerCallback` that handles the default flow of the training loop for logs, evaluation
    and checkpoints.
    """
```

I show you  DefaultFlowCallback, which is one of the examples of callback. You can see that we are subclassing TrainerCallback. I personally think that the callback provided by Huggingface can be classified into two types. I think there are callback classes provided by Huggingface by default and integration callback classes that are integrated with external services. If you enter the Huggingface repository, you can see that it is saved in two parts, trainer_callback.py and integrations.py. You can see that integrations.py is integrated with several metric logging services such as wandb, mlfow, and azure.

- Default Callback
  - DefaultFlowCallback
  - ProgressCallback
  - PrinterCallback
  - EarlyStoppingCallback
- Integration Callback
  - TensorboardCallback
  - WandbCallback
  - CometCallback
  - AzureMLCallback
  - MLFlowCallback
  - CodeCarbonCallback

These are the callback classes defined in the current repo.

## Callback Add and Remove

If a callback class is added, the callback is called whenever a specific condition is satisfied. In the case of Integration Callback, the method to add is specified in report_to argument when instantiating Trainer. For example, if you want to add WandBCallback, you can pass the argument "wandb". For Default Callbacks, DefaultFlowCallback is automatically added. To manually add callbacks, if you use the method called add_callback of Trainer, you can add callbacks. Callback can be deleted by using the method called remove_callback of Trainer.

## Callback Method

If you instantiate Trainer once in Huggingface, CallbackHandler, TrainerState, TrainerControl are designated as attributes of Trainer instance. You will call several methods as a callback handler. I don't know about other frameworks, but in the case of Huggingface, on_init_end, on_train_begin , on_step_end , .. etc. methods of callbacks are called at a specific time during training. It passes the metric, best_checkpoint, epoch, step, etc. when training with TrainerState, and specifies to execute the method called with boolean values such as should_evaluation , should_save, should_log, etc. with TrianserControl.



Let's see an example.

```python

                    model.zero_grad()
                    self.state.global_step += 1
                    self.state.epoch = epoch + (step + 1) / steps_in_epoch
                    self.control = self.callback_handler.on_step_end(args, self.state, self.control)
```

As an example, in the code above, you can see that the callback_handler calls a method called on_step_end whenever the step ends.

```python
    def on_step_end(self, args: TrainingArguments, state: TrainerState, control: TrainerControl):
        return self.call_event("on_step_end", args, state, control)
```

In this way, the on_step_end method calls a method called call_event.

```python
    def call_event(self, event, args, state, control, **kwargs):
        for callback in self.callbacks:
            result = getattr(callback, event)(
                args,
                state,
                control,
                model=self.model,
                tokenizer=self.tokenizer,
                optimizer=self.optimizer,
                lr_scheduler=self.lr_scheduler,
                train_dataloader=self.train_dataloader,
                eval_dataloader=self.eval_dataloader,
                **kwargs,
            )
            # A Callback can skip the return of `control` if it doesn't change it.
            if result is not None:
                control = result
        return control
```

For all callbacks added to Trainer in call_event, callbackhandler checks if there is a method corresponding to the event through getattr. Here, this method will be 'on_step_end'. Change the boolean values of TrainerControl . (Not all events will, but on_step_end changes the boolean value.)

```python

                    model.zero_grad()
                    self.state.global_step += 1
                    self.state.epoch = epoch + (step + 1) / steps_in_epoch
                    self.control = self.callback_handler.on_step_end(args, self.state, self.control)
                    self._maybe_log_save_evaluate(tr_loss, model, trial, epoch, ignore_keys_for_eval)
```

```python
    def _maybe_log_save_evaluate(self, tr_loss, model, trial, epoch, ignore_keys_for_eval):
        if self.control.should_log:
            logs: Dict[str, float] = {}

            # all_gather + mean() to get average loss over all processes
            tr_loss_scalar = self._nested_gather(tr_loss).mean().item()

            # reset tr_loss to zero
            tr_loss -= tr_loss

            logs["loss"] = round(tr_loss_scalar / (self.state.global_step - self._globalstep_last_logged), 4)
            logs["learning_rate"] = self._get_learning_rate()

            self._total_loss_scalar += tr_loss_scalar
            self._globalstep_last_logged = self.state.global_step
            self.store_flos()

            self.log(logs)

        metrics = None
        if self.control.should_evaluate:
            metrics = self.evaluate(ignore_keys=ignore_keys_for_eval)
            self._report_to_hp_search(trial, epoch, metrics)

        if self.control.should_save:
            self._save_checkpoint(model, trial, metrics=metrics)
            self.control = self.callback_handler.on_save(self.args, self.state, self.control)
```



After that, you will come back to the trainer's stackframe. And, through the method called maybe_log_save_evaluate, logging is done according to the attribute of the control. I haven't tried it yet, but by overriding the train method and _maybe_log_save_evaluate of the Trainer Class, logging can be tailored to the situation. However, if you are simply curious about the metric of the train data, I think it is more efficient to check whether overfitting by using the eval set as a subset of the train.

## Custom Callback

```python
class MyCallback(TrainerCallback):
    "A callback that prints a message at the beginning of training"

    def on_train_begin(self, args, state, control, **kwargs):
        print("Starting training")
```

```python
class PrinterCallback(TrainerCallback):
    """
    A bare :class:`~transformers.TrainerCallback` that just prints the logs.
    """

    def on_log(self, args, state, control, logs=None, **kwargs):
        print(logs)
        print(state.log_history)
        _ = logs.pop("total_flos", None)
        if state.is_local_process_zero:
            print(logs)
```

You can use the existing callbacks as they are, but you can customize and create your own callback class. The second example is an example where I newly defined PrinterCallback. However, when overriding a method, you must use the method defined in the callback handler class. As seen above, call_event is called in the method defined in callblackhandler. If a method that is not defined in the callbackhandler is overridden in the callback class, it will not be called.

