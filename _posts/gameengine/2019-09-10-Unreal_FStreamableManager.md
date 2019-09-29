---
layout: post
title: UnrealEngine FStreamableManager AssetLoader
categories: [GameEngine]
---

# FstreamableManager 구조체를 통한 UnrealEngine에서 런타임으로 애셋을 로딩

# UMyGameInstance.h
```c++
#pragma once

#include "CoreMinimal.h"
#include "Engine/GameInstance.h"
#include "Engine/StreamableManager.h"
#include "MyGameInstance.generated.h"

/**
 *
 */
UCLASS()
class ACTORTEST_API UMyGameInstance : public UGameInstance
{
	GENERATED_BODY()

public:
	 FStreamableManager AssetLoader;
};

```

# AABPawn.cpp

```c++
void AABPawn::BeginPlay()
{
	Super::BeginPlay();

	UE_LOG(LogClass, Warning, TEXT("%s"), *GetGameInstance()->GetName());

	UMyGameInstance* MyGameInstance = Cast<UMyGameInstance>(GetGameInstance());

	if (MyGameInstance)
	{
		FStringAssetReference asset("/Game/InfinityBladeWarriors/Character/CompleteCharacters/SK_CharM_Barbarous.SK_CharM_Barbarous");
		TAssetPtr<USkeletalMesh> NewCharacter = Cast<USkeletalMesh>(MyGameInstance->AssetLoader.SynchronousLoad(asset));

		if (NewCharacter)
		{
			Mesh->SetSkeletalMesh(NewCharacter.Get());
		}
	}
}
```