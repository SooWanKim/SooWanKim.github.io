---
layout: post
title: Unity blendshape 
categories: [C#, Unity]
---

Blender, Max, Maya에서 3D Model정보에 가중치값을 세팅한 blendshape key값을 유니티로 Export하여 사용할 수 있다.

나무애니메이션을 예로 들면 작은 나무의 모델이 Base key이고, 스케일 업된 나무의 모델이 shape key이면 가중치에 따라서 나무가 자라는 연출을 할 수 있다.

[Blender blend shape](https://www.youtube.com/watch?v=Uzw5zHLoyDM)

3D 모델에 별도의 애니메이션 key없이 Blender에서 shape key를 잡아 놓으면, 유니티에서 애니메이션을 연출 할 수 있다.

[blend shape 이름과 인덱스를 연결해주는 코드 튜토리얼](https://github.com/Glauz/Unity3D-CharacterCustomizationTutorial)

***

blendshape face animation in unity test 

https://flame.is.tue.mpg.de/download.php

얼굴을 스캔한 데이터로 학습한 모델을 통해 blend shape key값이 있는 3d face mesh를 제공한다. 

Blender add-on을 사용해서 blendshape key값이 있는 얼굴 메시를 자동으로 생성할 수 았으며 .fbx형태로 추출할 수 있다.

.fbx포맷은 유니티에서 사용할 수 있고, 모델 익스포팅 옵션 설정에 legacy blend shape normals 체크, 메시 압축 설정은 해제 한다.

skinned mesh renderer에 Blendshape 수치가 인스펙터상에서 slider를 통해 0에서100으로 밖에 수정이 안되지만

직접 입력하거나 스크립트상에서 편집하면 제한없이 입력할 수 있다. Blender에서 얼굴 메쉬에 들어간 blendshape 수치의 범위는 -3~3, 

유니티에서는 -300~300 정도가 비슷하게 표현된다.

***

Blender에서 추출한 얼굴메쉬를 게임에서 사용한다고 하면, 얼굴에 텍스쳐만 잘입히면 다양한 방법으로 사용할 수 있을거 같다.
blend shape key를 통해 다양한 형태의 얼굴을 blendshape key값만 조절해서 생성할 수 있으며
랜덤한 얼굴의 모델을 실시간, 혹은 편집으로 생성한다면 애셋 생성의 시간이 엄청 절약될 수 있다, 심지어 웃음, 분노, 기쁨등 얼굴로 표현되는
애니메이션을 자동으로 생성된 모든 모델에 적용할 수 있으니 엄청난 시간절약이 된다.

얼굴만 사용하는게 아니라 몸통 모델링까지 붙이고, 기본 애니메이션은 key를 잡아서 생성하고 얼굴 애니메이션은 blend shape를 사용해서 연출하는 것이다. 
