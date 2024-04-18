#wanDB Sweep のパラメータ設定

### name:
sweep全体の名前です．

### schedular:
これをつけるとoptunaの設定ができます．  
例：CMAESamplerを使う場合
```
scheduler:
  job: wandb/sweep-jobs/job-optuna-sweep-scheduler:latest
  resource: local-container
  num_workers: 1 # 同時実行されるトライアル数

  # Optunaの設定
  settings:
    pruner:
      sampler:
        # type: TPESampler
        type: CMAESampler
        n_startup_trials: 5
      type: PercentilePruner
      args:
        percentile: 25.0 # 下位75%のトライアルを削除
        n_warmup_steps: 10 # 最初の10ステップでプルーニングを無効化
```

### metric:
sweepの基準値を設定します．
wandb_logに渡している変数から選択します．
例：
```
metric:
  name: Binary Test F1-Score    #対象（wandb_logしてる値から選択）
  goal: maximize                #目的値（最大化，最小化，etc...）
```

### parameters:
wandb.configに格納されるパラメータです．
ここのパラメータが実行ごとに調整されます．
例：
```
parameters:
  learning_rate:    # 数値の範囲指定をする場合
    min: 5.0e-10
    max: 5.0e-5
  num_epochs:       # 複数の値から選択する場合
    values: [1, 3, 5, 7, 9]
  batch_size:
    values: [16, 32, 64]
  sentences         # リスト，文字列の場合
    values: [
        None,
        ['こんにちは', 'こんばんは'],
        ['さよなら', 'またね']
    ]
```