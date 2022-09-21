# Conda 환경 기반 Horovod 설치방법

Horovod는 고성능 분산 컴퓨팅 환경에서 노드간 메시지 전달 및 통신관리를 위해 일반적인 표준 MPI 모델을 사용하며, Horovod의 MPI구현은 표준 Tensorflow 분산 훈련 모델보다 간소화된 프로그래밍 모델을 제공한다. NEURON시스템에서도 콘다 환경을 기반으로 멀티노드를 이용한 훈련 모델을 학습시키고자 한다면 다음과 같은 방법으로 설치 후 실행할 수 있다.

\- Horovod 사용법은 \[별첨8] 참고.

## 1. Tensorflow-horovod 설치

1\) 콘다 환경 생성

| <p>$ module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1 cmake/3.16.9</p><p>$ conda create -n my_tensorflow</p><p>$ source activate my_tensorflow</p><p>(my_tensorflow) $</p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

\-자세한 콘다 사용방법은 \[별첨 5] 참고



2\) Tensorflow 설치 및 horovod 설치

| <p>(my_tensorflow) $ conda install tensorflow-gpu=2.0.0 tensorboard=2.0.0 tensorflow-estimator=2.0.0 python=3.7 cudnn cudatoolkit=10 nccl=2.8.3</p><p>(my_tensorflow) $ HOROVOD_WITH_MPI=1 HOROVOD_GPU_OPERATIONS=NCCL HOROVOD_NCCL_LINK=SHARED HOROVOD_WITH_TENSORFLOW=1</p><p>pip install --no-cache-dir horovod==0.23.0</p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |



3\) Horovod 설치 확인

| <p></p><p>(my_tensorflow) $ pip list | grep horovod</p><p>horovod 0.23.</p><p>(my_tensorflow) $ python</p><p>>>> import horovod</p><p>>>> horovod.__<em>version__</em></p><p> '0.23.0'</p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |



4\) Horovod 실행 예시

4-1) interactive 실행 예시

| <p>$ salloc --partition=cas_v100_4 -J debug --nodes=2 --ntasks-per-node=2 --time=08:00:00 --gres=gpu:2 --comment=etc<br>$ echo $SLURM_NODELIST</p><p><strong>gpu[12-13]</strong></p><p>$ module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1</p><p>$ source activate my_tensorflow (my_tensorflow)</p><p>$ horovodrun -np 4 -H <strong>gpu12:2,gpu13:2</strong> python tensorflow2_mnist.py</p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |



4-2) batch 스크립트 실행 예시

|                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>#!/bin/bash</p><p>#SBATCH -J test_job</p><p>#SBATCH -p cas_v100_4</p><p>#SBATCH -N 2</p><p>#SBATCH --ntasks-per-node=2</p><p>#SBATCH -o %x.o%j</p><p>#SBATCH -e %x.e%j</p><p>#SBATCH --time 00:30:00</p><p>#SBATCH --gres=gpu:2</p><p>#SBATCH --comment etc</p><p></p><p>module purge module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1</p><p></p><p>source activate my_tensorflow</p><p></p><p>horovodrun -np 2 python tensorflow2_mnist.py</p> |



## 2. Pytorch-horovod 설치

1\) 콘다 환경 생성

| <p>$ module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1 cmake/3.16.9</p><p>$ conda create -n my_pytorch</p><p>$ source activate my_pytorch</p><p>(my_pytorch) $</p> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |



2\) Pytorch 설치 및 horovod 설치

| <p>(my_pytorch) $ conda install pytorch=1.11.0 python=3.9 torchvision=0.12.0 torchaudio=0.11.0 cudatoolkit=10.2 -c pytorch</p><p>(my_pytorch) $ HOROVOD_WITH_MPI=1 HOROVOD_NCCL_LINK=SHARED HOROVOD_GPU_OPERATIONS=NCCL HOROVOD_WITH_PYTORCH=1</p><p>pip install --no-cache-dir horovod==0.24.0</p> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |



3\) Horovod 설치 확인

| <p>(my_pytorch) $ pip list | grep horovod</p><p> horovod 0.24.0</p><p></p><p>(my_pytorch) $ python</p><p>>>> import horovod</p><p>>>>  horovod.__<em>version__</em></p><p>'0.24.0'</p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |



4\) Horovod 실행 예시

4-1) interactive 실행 예시

| <p>$ salloc --partition=cas_v100_4 -J debug --nodes=2 --ntasks-per-node=2 --time=08:00:00 --gres=gpu:2 --comment=etc</p><p>$ echo $SLURM_NODELIST</p><p><strong>gpu[22-23]</strong></p><p>$ module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1</p><p>$ source activate my_pytorch</p><p>(my_pytorch) $ horovodrun -np 4 -H <strong>gpu22:2,gpu23:2</strong> python pytorch_ex.py</p> |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |



4-2) batch 스크립트 실행 예시

| <p>#!/bin/bash</p><p>#SBATCH -J test_job</p><p>#SBATCH -p cas_v100_4</p><p>#SBATCH -N 2</p><p>#SBATCH --ntasks-per-node=2</p><p>#SBATCH -o %x.o%j</p><p>#SBATCH -e %x.e%j</p><p>#SBATCH --time 00:30:00</p><p>#SBATCH --gres=gpu:2</p><p>#SBATCH --comment etc</p><p></p><p>module purge</p><p>module load gcc/7.2.0 cuda/10.1 cudampi/openmpi-3.1.5 python/3.7.1</p><p></p><p>source activate my_pytorch</p><p></p><p>horovodrun -np 2 python pytorch_ex.py</p> |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

{% hint style="info" %}
2022년 7월 28일에 마지막으로 업데이트되었습니다.
{% endhint %}
