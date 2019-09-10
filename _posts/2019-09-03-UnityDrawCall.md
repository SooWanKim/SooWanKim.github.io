---
layout: post
title: Unity Draw Call
categories: [GameEngine]
---


# Dynamic Batching

DynamicBatching은 Per VertexOverHead가 발생(**Batching**되는 Object의 Vertex가 많으면 CPU부하가 발생한다는 뜻)

Unity에서는 모두 합쳐서 900개의 VertexAttribute이하의 Object에 대해서만 DynamicBatching이 발생하도록 설정

예를 들어 쉐이더가 VertexPos, Normal, UV Coordinate까지 가지고 있다면 각각 300, 300, 300개의 Vertex를 가진 Object가 DynamicBathcing의 한계가 되고 UV0, UV1에 TangentVector까지 포함한다면 900/5 즉 180개의 Vertex를 가진 Object가 DynamicBatching의 한계가 된다. 제한값은 바뀔수 있다고함

일반적으로 Object가 동일한 Scale을 가져야 DynamicBatching이 일어난다. 다른 Material Instance를 사용할경우 (동일한 Material을 사용한다고 할지라도) DynamicBatching이 발생하지 않는다.

MultiPassShader를 적용한 Object는 Batching이 되지 않는다.


# Static Batching

StaticBatching의 사용 조건은 ** 이동이 없어야 하고, 동일한 Material사용, 회전하거나 스케일하는것이 없다** 로 되어야함. (Object가 Static임을 표시해줘야한다. ) Inspector에 있음

StaticBatching을 사용해서 하나로 묶인 Geometry를 메모리에 저장해야 되기 때문에 추가적인 메모리를 사용하게된다. 크기가 큰 StaticBatching는 메모리 부족을 초래할수도 있다고 함

Shader의 pass하나당 drawcall 하나가 올라감

![](/assets/images/2019-09-03-UnityDrawCall/2019-09-03-14-42-35.png)

# UI Atlas

총 같은 Atlas의 이미지가 총3장 있고 순서대로 그려지는 상태(1 DrawCall )에서 중간에 다른 Atlas의 이미지가 들어가게 되면 DrawCall이 3된다.

# Material

배칭될 Material을 코드에서 참조하거나 ex) Material material = render.mateial,  SkinnedMeshRenderer을 사용하면

Material이 배칭되지 않고 Instance가 생성 되어 개별로 그려진다.

SkinnedMeshRenderer은 MeshRenderer로 변경하고, MeshFilter를 추가하면 해결 됨