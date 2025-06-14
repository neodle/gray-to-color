# Image colorization with pix2pix (pix2pix를 활용한 이미지 색상화)
---


# CONTENTS


* **Motivation** : 프로젝트의 주제 선정 및 배경

  
* **Dataset** : 프로젝트에 사용한 데이터 셋 소개

  
* **Model Architecture** : pix2pix 모델 소개


* **Model Analysis** : 모델 분석 결과

  
* **Model Improvement** : 분석 결과를 토대로 모델 성능 향상


* **Service deploy** : 서비스 방안 소개


* **future works** : 향후 계획
---


# Motivation

최근 AI 기술의 발달로 과거의 기록물들을 디지털로 복원하려는 수요가 증가하고 있음

흑백 사진은 정보 전달력과 감성 전달 측면에서 한계를 가지며, 이를 색상화 함으로써 시각적 이해도와 몰입도와 생동감을 높일 수 있음

하지만 기존의 수작업으로 진행되던 색상화 방식은 시간과 비용이 많이 듬

이에 본 프로젝트에서는 기존의 색상화 작업보다 시간과 비용 측면에서 효율적인 딥러닝을 이용한 색상화 방식에 대해 연구를 진행
  
자연스러운 복원 결과를 위해 시각적 유사성을 평가하는 성능 평가 지표 뿐만 아니라 사용자 주관적 평가도 함께 고려함

이를 통해 역사적 기록물, 사진 복원, 교육용 자료등 다양한 분야에 활용할 수 있으며, 더나아가 개인의 소중한 기억의 한 조각을 찾는 효과를 기대할 수 있음 

<p align="center">
  <img src="https://github.com/user-attachments/assets/4c2ef17c-7355-46f5-8ad2-bebae5f75b61" width="700" />
</p>


--- 

# **Project object**

**프로젝트 목표:** 딥러닝 모델을 활용한 흑백 사진을 자연스럽고 실사에 가까운 컬러 이미지로 복원하는 AI 시스템 구현


--- 


# **Flowchart** 

<p align="center">
  <img src="https://github.com/user-attachments/assets/4d278af9-5f86-477f-bb96-d5dcee76fed9" width="700" />
</p>

---


# Dataset

## 데이터 전처리 과정 및 순서

I. 데이터 셋 수집

- 다양한 쌍이미지 기반 이미지 데이터 셋 수집 (다양한 풍경, 인물, 동물, 꽃, 음식 등)

II. 데이터 EDA (탐색적 데이터 분석) 

-  EDA 이후, 최종 데이터 셋 결정 
-  최종 데이터 셋 결정 후 프로젝트에 쓰이는 데이터 셋 생성
-  **데이터셋 구성** [Train (color 1500 : gray-scale 1500)] : [Validation (color 100 : gray-scale 100)] : Test [gray-scale 150]

III. EDA 분석 

- 컬러 이미지 데이터와 흑백 이미지 데이터 높이(Height)와 너비(width) 분포

| Gray-sclae img EDA | Color img EDA |
|---------|---------|
| <img src= "https://github.com/user-attachments/assets/c6f8657a-f4f0-4979-ba07-77073cb719b3" width="550" height="420" /> | <img src= "https://github.com/user-attachments/assets/f89caa2f-705a-43a0-98b6-4a7c5fbc8bd9" width="550" height="420" /> |

- Color 이미지 데이터의 RGB channel 분포

<p align="center">
  <img src="https://github.com/user-attachments/assets/cd87f8b7-a9b7-462c-aece-c277cec31b6b" width="700" />
</p>

**컬러 이미지**와 **흑백 이미지** 모두 **150×150 크기**의 정사각형 이미지 데이터

**컬러 이미지**에서 **R, G, B** 세 채널 모두 대체로 균일하게 분포되어 색상의 균형이 잘 맞춰져있음

IV. 데이터 전처리 

- 모든 이미지가 동일한 크기를 가지므로 모델 학습에 앞서 별도의 리사이징 과정 및 이미지 데이터 전처리 과정이 필요하지 않음

---

# Model Architecture  

**Pix2Pix** 

## Pix2Pix - U-Net based Generator + PatchGAN Discriminator

<p align="center">
<img src="https://github.com/user-attachments/assets/92efdf19-051b-421b-9006-aa8322b542e5" width="700" height="400" />
</p>

## Pix2Pix 모델 선정 배경 및 구조

## Pix2Pix 모델 선정 배경

**1. 흑백 이미지 → 컬러 이미지 색상화 픽셀 단위의 정밀한 매핑이 필요한 image-to-image translation 문제** 

**2. U-Net 구조는 인코더(압축)와 디코더(복원) 사이에 skip connection이 있음 → 저수준의 공간 정보를 고해상도로 정확히 복원할 수 있음**

**3. U-Net 구조 덕분에 색상화 시 중요한 윤곽선, 질감, 경계 정보가 잘 유지됨**

**4. 일반적인 Discriminator는 이미지 전체를 보고 Real/Fake를 구분**

**5. 색상화 문제에서는 국소 영역(작은 패치)의 색 배치나 질감이 자연스러운지가 더 중요함**

**6. PatchGAN은 전체 이미지를 하나로 처리하지 않고, 공통된 소형 CNN 필터를 반복 적용하여 패치를 평가함 → 모델 파라미터 수가 적고 속도도 빠르며, 학습이 안정적임**

**<결론>** U-Net based Generator와 PatchGAN Discriminator가 결합된 구조인 pix2pix 모델을 선정 

## U-Net based Generator

**1. 인코더(encoder) 부분에서 입력 이미지가 점점 축소되며 특징을 추출함** 

**2. 디코더(decoder) 부분에서 이미지에 대한 해상도를 다시 복원함**

**3. 점선으로 이어진 화살표는 입력 정보와 출력 정보를 직접 연결하는 U-Net의 핵심인 스킵 커넥션이며, 저수준 정보를 고수준 레이어에 직접 전달해 디테일 유지함**

## Patch GAN Discriminator

**1. U-Net 기반의 Generator (T) 부분에서 생성한 결과 이미지인 T(x)와 정답 이미지인 Ground Truth y 두개의 이미지 이용해 판별에 사용** 

**2. CNN 기반으로 점점 공간의 크기를 줄여가며 특징 추출을 하며, P1, P2, P3, P4의 여러 단계에서 T(x)와 Ground Truth y 쌍의 정합성을 확인함**

**3. 마지막 출력에서 실제 이미지인지, Generator가 만든 가짜 이미지인지 판단**

**4. 판별 결과를 바탕으로 Generator는 더 정답과 비슷한 T(x)를 만들도록 학습됨**

---

# Model Analysis 

## 모델의 문제점

<p align="center">
<img src= "https://github.com/user-attachments/assets/75ccf1ca-92ff-4786-a7ea-92f09f820bd6" width="700" height="400" />
</p>

**1.** 결과 이미지(Result Image)가 원본 이미지와 비교했을 때 품질이 좋지 않음

**2.** 성능지표 또한 수치가 높게 나오지 않음

**성능지표(Evaluation metrix)**

**1. PSNR** - Peak Signal-to-noise ratio

**2. SSIM** - Structural Similarity Index Measure

**3. LPIPS** – Learned Perceptual Image Patch Similarity

**4. FID** - Fréchet inception distance

## 모델의 성능 저하의 원인

**1. 하이퍼 파라미터 조정** : PSNR, SSIM이 모두 낮고 LPIPS, FID이 모두 높다면 학습이 부족하다는 의미임 (현재 epoch 10회)

**2. G_LOSS** - 학습 결과 Discriminator의 loss가 일정하게 낮은 반면, Generator의 loss는 epoch이 증가하여도 감소하지 않고 높은 수준(10 ~ 12)에서 진동함

| Gray-sclae img EDA | Color img EDA |
|---------|---------|
| <img src= "https://github.com/user-attachments/assets/358dd685-c8a1-47c1-81fa-5069cb44206d" width="550" height="420" /> | <img src= "https://github.com/user-attachments/assets/f6f4919b-dbac-46fc-a46d-7c7078365128" width="550" height="420" /> |

# Model Improvement

**1. 하이퍼 파라미터 조정** : epoch 50회, early-stopping, Learning rate 조정

**2. G_LOSS** - 학습 결과 Discriminator의 loss가 일정하게 낮은 반면, Generator의 loss는 epoch이 증가하여도 감소하지 않고 높은 수준(10 ~ 12)에서 진동함





---

# 프로젝트의 한계 및 향후 연구 방향

---

**1. 학습 데이터 추가 및 증강 시도**

다양한 환경에서의 도로의 차선 데이터를 학습하여, 속도나 조명, 날씨 관계에 따른 변화에도 안정적인 탐지가 가능할 수 있도록 개발한다. (데이터 증강 시도)

**2. 훼손된 차선 경고 시스템**

차선 훼손 탐지 알고리즘은 도로노면 훼손 정보를 실시간으로 수집하기에 좋은 수단이다.

이를 통해 탐지 관련 노이즈 특성이 강하게 나타나는 경우 운전자에게 경고 시스템으로 제공하는 환경을 조성한다.

---

# 기대효과

**1. 블랙박스를 활용한 차선 훼손 탐지 기술 개발**

블랙박스 카메라와 Yolov8의 결합으로 실시간 포트홀 탐지 시스템을 개발할 수 있다.

도로의 포트홀과 차선 훼손을 신속하고 정확하게 탐지할 수 있다.

**2. 데이터 기반 실시간 도로 관리 시스템**

실시간 탐지를 통해 축적된 데이터를 바탕으로 도로 관리 시스템의 성능을 향상한다.

도로 유지 보수 작업의 우선순위를 효율적으로 설정하 수 있는 기반을 마련한다.

도로 유지 보수의 자동화 및 지능화를 촉진 시킬 수 있다.

**3. 도로 안전성 향상**

차선 훼손 탐지 및 대응으로 도로 이용자들의 안전을 크게 향상 시킬 수 있다. 

교통사고 발생률을 줄이고, 도로 이용자들에게 보다 안전한 운전 환경을 제공한다.

**4. 경제적 비용 절감**

차량 소유주와 보험사, 도로관리 당국의 경제적 부담을 경감시킬 수 있다.

인건비를 크게 들이지 않고, 효율적인 도로 유지 보수로 인해 국가 또는 지역 사회의 도로 관리 비용을 절감한다.

**5. 공공 서비스의 개선**

도로 관리의ㅣ 효율성 향상으로 도로 이용자들은 더 나은 도로를 제공 받으며, 도로 관리 당국의 신뢰성을 높이고, 공공 인프라에 대한 만족도를 증대시킬 수 있다.
