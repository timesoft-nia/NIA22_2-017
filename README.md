# NIA 2022 2-017 뉴스 대본 및 앵커 음성 데이터

### 모델 명칭
* VITS<br>

### 모델 버전
* 1.0<br>

### 모델 설명<br>
* 구축되는 학습데이터를 활용하여 발화자의 목소리 구분을 위해 Stochastic Duration Predictor 알고리즘과 음성을 분석하는 Encoder와 음성을 생성하는 Decoder를 이어주는 VAE 알고리즘을 적용하여 End-to-End 음성합성 학습모델<br>

### 모델 구조 및 Task<br>

VITS at training|VITS at inference|
---|---|
![fig_1a](https://user-images.githubusercontent.com/118957399/205776786-32a06d0b-d1e5-47df-a164-7724331707b7.png)|![fig_1b](https://user-images.githubusercontent.com/118957399/205776793-1b22ff0e-2b7f-4b5a-b121-52d543bec02a.png)|

### Input Shape
  * (x, x_lengths, y, y_lengths, waveform, aux_input={'d_vectors': None, 'language_ids': None, 'speaker_ids': None})<br>
    x: [B,Tseq]<br>
    x_lengths: [B]<br>
    y: [B,C,Tspec]<br>
    y_lengths: [B]<br>
    waveform: [B,1,Twav]<br>
    d_vectors: [B,C,1]<br>
    speaker_ids: [B]<br>
    language_ids: [B]<br>

### Output Shape<br>
model_outputs: [B,1,Twav]<br>
alignments: [B,Tseq,Tdec]<br>
z: [B,C,Tdec]<br>
z_p: [B,C,Tdec]<br>
m_p: [B,C,Tdec]<br>
logs_p: [B,C,Tdec]<br>

### 학습 데이터셋<br>

WAV|TXT|
---|---|
381,456 개|381,456 개|

* WAV 형식 조건: Mono/22050Hz<br>
* TXT 형식 조건: 음원 위치|발화자 번호|스크립트 <br>

### 학습 하이퍼파라미터<br>
  * train: {
    "log_interval": 200,
    "eval_interval": 1000,
    "seed": 1234,
    "epochs": 10000,
    "learning_rate": 2e-4,
    "betas": [0.8, 0.99],
    "eps": 1e-9,
    "batch_size": 32,
    "fp16_run": false,
    "lr_decay": 0.999875,
    "segment_size": 8192,
    "init_lr_ratio": 1,
    "warmup_epochs": 0,
    "c_mel": 45,
    "c_kl": 1.0
  }<br><br>
  * data: {
    "training_files":"filelists/nia22_audio_text_train_filelist.txt.cleaned",
    "validation_files":"filelists/nia22_audio_text_val_filelist.txt.cleaned",
    "text_cleaners":["korean_cleaners"],
    "max_wav_value": 32768.0,
    "sampling_rate": 22050,
    "filter_length": 1024,
    "hop_length": 256,
    "win_length": 1024,
    "n_mel_channels": 80,
    "mel_fmin": 0.0,
    "mel_fmax": null,
    "add_blank": true,
    "n_speakers": 100,
    "cleaned_text": true
  }<br><br>
  * model: {
    "inter_channels": 192,
    "hidden_channels": 192,
    "filter_channels": 768,
    "n_heads": 2,
    "n_layers": 6,
    "kernel_size": 3,
    "p_dropout": 0.1,
    "resblock": "1",
    "resblock_kernel_sizes": [3,7,11],
    "resblock_dilation_sizes": [[1,3,5], [1,3,5], [1,3,5]],
    "upsample_rates": [8,8,2,2],
    "upsample_initial_channel": 512,
    "upsample_kernel_sizes": [16,16,4,4],
    "n_layers_q": 3,
    "use_spectral_norm": false,
    "gin_channels": 256
  }<br>

### 학습 평가지표<br>
* MOS(Mean Opnion Score) 3.5 이상 목표

### 모델 라이센스
* [MIT](https://opensource.org/licenses/MIT)
