---
layout: post
title: RadialBlurShader 
---

CenterPos 중심으로 Blur를 설정한다.
_Samples는 샘플링 수, _BlurSize는 블러를위해 이미지가 이동한 거리  
_MainTex_TexelSize는 하나의 텍셀이 차지하는 크기, 여기서의 _MainTex = ScreenSize 이기 때문에 가로가 1024라면 1/1024 의 값

# Shader code
```markdown
Shader "ImageEffect/RadialBlur"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        // No culling or depth
        Cull Back ZWrite Off ZTest Always

        Pass
        {
            CGPROGRAM
            #pragma vertex vert_img
            #pragma fragment frag
			#pragma	target 3.0

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

          /*  v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = v.uv;
                return o;
            }*/

            sampler2D _MainTex;
			half4 _MainTex_TexelSize;
			half2 _BlurCenterPos;
			half _BlurSize;
			half _Samples;

            fixed4 frag (v2f_img IN) : SV_Target
            {
			   half4 col = half4(0,0,0,1);
			   half2 movedTexcoord = IN.uv - _BlurCenterPos;

			   for (int i = 0; i < _Samples; i++)
			   {
				   half Scale = 1.0f - _BlurSize * _MainTex_TexelSize.x * i;
				   col.rgb += tex2D(_MainTex, movedTexcoord * Scale + _BlurCenterPos).rgb;
			   }

			   col.rgb *= 1 / _Samples;

			   return col;
            }
            ENDCG
        }
    }
}
```

# CS Code
```markdown
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RadialBlurEffect : MonoBehaviour
{
    public float blurSize = 0.1f;

    public Vector2 blurCenterPos = new Vector2(0.5f, 0.5f);
    [Range(1, 48)]
    public int samples;

    public Material radialBlurMaterial = null;
    // Start is called before the first frame update
    
    private void OnRenderImage(RenderTexture source, RenderTexture destination)
    {
        if(blurSize > 0.0f)
        {
            radialBlurMaterial.SetInt("_Samples",samples);
            radialBlurMaterial.SetFloat("_BlurSize",blurSize);
            radialBlurMaterial.SetVector("_BlurCenterPos",blurCenterPos);
            Graphics.Blit(source, destination, radialBlurMaterial);
        }
        else
        {
            Graphics.Blit(source, destination, radialBlurMaterial);
        }
    }
}


```
