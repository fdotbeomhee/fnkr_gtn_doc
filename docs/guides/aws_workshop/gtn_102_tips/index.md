# GTN 102: 유용한 팁 {: #gtn-102-useful-tips }

## 1. 유용한 노드 및 라이브러리 (Useful Nodes & Libraries) {: #useful-nodes-libraries }

   1. SAM3  
      1. https://github.com/griptape-ai/griptape-nodes-library-sam3

      2. Cinematic Director

         

      3. Annotate Image  
         1. https://github.com/griptape-ai/griptape-nodes-library-annotate
## 2. 반복 노드, 워크플로우 배포 및 로컬 파이프라인 {: #loops-publish-local-pipelines }  
   1. OCIO, EXR  
      1. 라이브러리 설치  
         1. https://github.com/griptape-ai/griptape-nodes-library-opencolorio  
         2. https://github.com/griptape-ai/griptape-nodes-library-openexr  
      2. OCIO config 설정하기  
      3. EXR불러오기  
      4. EXR 워크플로우

단일 EXR 파일 → 레퍼런스 이미지  
Load EXR → Display EXR Part → Seedance 2.5 Reference Images

* Display EXR Part 노드는 EXR을 8비트 sRGB PNG로 톤 매핑(tone-map)합니다. exposure(노출) 및 tone_mapping(filmic/linear)을 설정하거나 OCIO 컬러 매니지먼트를 사용하세요.  
    
* 프롬프트에서는 이를 @Image 1로 지칭하세요.


EXR 시퀀스 → 레퍼런스 비디오  
Scan Sequence → EXR Sequence To Preview → Seedance 2.5 Reference Videos

* EXR Sequence To Preview 노드는 각 프레임을 톤 매핑하고 해당 시퀀스를 MP4/MOV로 인코딩합니다. 해당 노드에서 frame_rate, part_index, exposure 및 color mode를 설정하세요.  
    
* 클립 길이는 2~30초여야 하며, 모든 참조 비디오의 총합은 30초를 초과할 수 없습니다.  
    
* Seedance는 URL 형태의 비디오만 수용하므로, 노드가 클립을 Griptape Cloud에 자동으로 업로드합니다.  
    
* 프롬프트에서는 이를 @Video 1로 지칭하세요.


두 경우 모두: Task를 Reference to Video로 설정하세요. 참조 입력은 해당 Task 및 Video Editing/Extension에만 적용됩니다. 제한 사항은 이미지 30개, 비디오 10개, 오디오 클립 10개입니다.

Seedance는 톤 매핑된 8비트 버전만 인식하므로 HDR/float(부동소수점) 데이터는 손실됩니다. 변환 전에 노출을 조정하여 원하는 룩을 만드세요.

현재 노드로 제공되는 생성 모델(Seedance, Runway, diffusers 파이프라인 등) 중 EXR이나 16비트 float을 참조로 사용하는 모델은 없습니다. 모두 8비트 이미지나 비디오를 사용하므로 톤 매핑 단계가 반드시 필요합니다.

Float 데이터를 유지하는 예외 케이스:

* CorridorKey Video Inference (생성이 아닌 키잉 작업): Scan Sequence를 통해 EXR 시퀀스를 직접 읽고 float32 EXR 시퀀스로 다시 출력할 수 있습니다 (output_format: exr_sequence).  
    
* 생성 결과물에서 HDR을 얻는 방법: Decode HDR Latents with LTX 2.3 HDR은 로우(raw) 리니어 EXR 프레임을 저장할 수 있습니다 (exr_output_folder). RunwayML Video to HDR은 SDR 클립을 HDR10, HLG 또는 ProRes로 변환합니다. Runway의 EXR 시퀀스 출력은 아직 노드에서 지원되지 않습니다.


따라서 일반적인 워크플로우는 EXR → 톤 매핑된 SDR 참조 → 생성 → 필요 시 HDR로 다시 업컨버팅 과정으로 진행됩니다.

**Single EXR → reference image**

Load EXR → Display EXR Part → Seedance 2.5 **Reference Images**

* Display EXR Part tone-maps the EXR down to an 8-bit sRGB PNG. Set exposure and tone_mapping (filmic/linear), or use OCIO color management.  
* In the prompt, refer to it as @Image 1.

**EXR sequence → reference video**

Scan Sequence → EXR Sequence To Preview → Seedance 2.5 **Reference Videos**

* EXR Sequence To Preview tone-maps each frame and encodes the sequence to MP4/MOV. Set frame_rate, part_index, exposure and color mode on that node.  
* The clip has to be 2–30s, and all reference videos together can't be more than 30s.  
* Seedance only accepts video by URL, so the node uploads the clip to Griptape Cloud for you.  
* In the prompt, refer to it as @Video 1.

**Both cases:** set **Task** to Reference to Video. The reference inputs only apply to that task and to Video Editing/Extension. Limits are 30 images, 10 videos and 10 audio clips.

Seedance only ever sees the 8-bit tone-mapped version, so the HDR/float data is lost. Adjust exposure before converting so the image looks the way you want.

None of the generation models we have nodes for (Seedance, Runway, the diffusers pipelines, etc.) take EXR or 16-bit float as a reference. They all use 8-bit images or video, so you still need the tone-map step.

What does keep the float data:

* **CorridorKey Video Inference** (keying, not generation) reads an EXR sequence directly via Scan Sequence and can write a float32 EXR sequence back out (output_format: exr_sequence).  
* **To get HDR out of a generation:** **Decode HDR Latents** with LTX 2.3 HDR can save raw linear EXR frames (exr_output_folder). **RunwayML Video to HDR** turns an SDR clip into HDR10, HLG or ProRes. Runway's EXR-sequence output isn't supported in the node yet.

So the usual path is EXR → tone-mapped SDR reference → generate → convert back up to HDR if you need it.

2. Iterate workflow  
   1. Make various result easily: Style transfer  
      2. Loop  
         1. Basic structure  
         2. Teach how to run loop all at once

   3. Diffusion Pipelines & Modular Diffusion Pipeline  
      1. https://docs.griptapenodes.com/en/stable/nodes/advanced_media_library/diffusion_pipelines/  
      2. https://docs.griptapenodes.com/en/stable/libraries/diffusers/  
      3. Modular Diffusion Pipeline

각 빌더가 제공하는 베이스 모델 목록 및 Hugging Face 기준 크기(GB, $1{0}^{9}$ 바이트)입니다.

* **Weights(가중치):** 파이프라인이 로드하는 safetensors 파일 (모든 구성 요소 포함)  
* **Repo(저장소):** 전체 HF 저장소 (추가 단일 파일이나 fp16 사본이 포함된 경우가 많음)  
* **DPB:** Diffusion Pipeline Builder (advanced-media 라이브러리)  
* **MPB:** Modular Diffusion Pipeline Builder (diffusers 라이브러리)

base models each builder offers, with sizes from Hugging Face in GB (10⁹ bytes). 

* **Weights** = the safetensors a pipeline loads (all components)  
* **Repo** = the whole HF repo, which often also holds extra single-file or fp16 copies  
* DPB = Diffusion Pipeline Builder (advanced-media library)  
* MPB = Modular Diffusion Pipeline Builder (diffusers library).


| Model | Weights(GBs) | Repo(GBs) | DPB | MPB |
| :---- | ----: | ----: | :---- | :---- |
| amused/amused-256 | 3.5 | 5.3 | ✓ |  |
| amused/amused-512 | 3.5 | 5.3 | ✓ |  |
| black-forest-labs/FLUX.1-dev | 33.7 | 57.9 | ✓ | ✓ |
| black-forest-labs/FLUX.1-Fill-dev | 33.9 | 58.1 | ✓ | ✓ |
| black-forest-labs/FLUX.1-Kontext-dev | 33.7 | 57.9 | ✓ | ✓ |
| black-forest-labs/FLUX.1-Krea-dev | 33.7 | 57.9 | ✓ | ✓ |
| black-forest-labs/FLUX.1-schnell | 33.7 | 57.8 | ✓ | ✓ |
| black-forest-labs/FLUX.2-dev | 112.8 | 177.6 | ✓ | ✓ |
| black-forest-labs/FLUX.2-dev-NVFP4 ¹ | 21 | 43.8 | ✓ | ✓ |
| diffusers/FLUX.2-dev-bnb-4bit | 33.9 | 33.9 | ✓ | ✓ |
| fal/FLUX.2-dev-Turbo ² | 2.8 | 5.5 | ✓ | ✓ |
| black-forest-labs/FLUX.2-klein-4B | 16 | 23.7 | ✓ | ✓ |
| black-forest-labs/FLUX.2-klein-base-4B | 16 | 23.7 | ✓ | ✓ |
| black-forest-labs/FLUX.2-klein-9B | 34.7 | 52.9 | ✓ | ✓ |
| black-forest-labs/FLUX.2-klein-base-9B | 34.7 | 52.9 | ✓ | ✓ |
| CompVis/stable-diffusion-v1-1 / v1-2 / v1-3 | 2.7 each | 11.0 each | ✓ |  |
| CompVis/stable-diffusion-v1-4 | 5.5 | 23.3 | ✓ |  |
| stabilityai/stable-diffusion-2 / 2-1 ³ | — | — | ✓ |  |
| stabilityai/stable-diffusion-3-medium-diffusers | 25 | 31 | ✓ | ✓ |
| stabilityai/stable-diffusion-3.5-medium | 42.1 | 48.9 | ✓ | ✓ |
| stabilityai/stable-diffusion-3.5-large | 53.5 | 71.6 | ✓ | ✓ |
| stabilityai/stable-diffusion-3.5-large-turbo | 53.5 | 71.6 | ✓ | ✓ |
| stabilityai/stable-diffusion-xl-base-1.0 | 14.5 | 76.9 |  | ✓ |
| stabilityai/stable-diffusion-xl-refiner-1.0 | 12.5 | 30.9 |  | ✓ |
| diffusers/stable-diffusion-xl-1.0-inpainting-0.1 | 13.9 | 20.8 |  | ✓ |
| stabilityai/stable-video-diffusion-img2vid-xt (DepthCrafter) | 9 | 32.6 | ✓ |  |
| Qwen/Qwen-Image | 57.7 | 57.7 | ✓ | ✓ |
| Qwen/Qwen-Image-Edit | 57.7 | 57.7 | ✓ | ✓ |
| Qwen/Qwen-Image-Edit-2509 | 57.7 | 57.7 | ✓ | ✓ |
| Qwen/Qwen-Image-Edit-2511 | 57.7 | 57.7 | ✓ | ✓ |
| Qwen/Qwen-Image-Layered | 57.7 | 57.7 | ✓ |  |
| Tongyi-MAI/Z-Image-Turbo | 32.8 | 32.9 | ✓ | ✓ |
| zai-org/GLM-Image | 35.8 | 35.8 | ✓ |  |
| Wan-AI/Wan2.1-T2V-1.3B-Diffusers | 28.9 | 28.9 | ✓ | ✓ |
| Wan-AI/Wan2.1-T2V-14B-Diffusers | 80.4 | 80.4 | ✓ | ✓ |
| Wan-AI/Wan2.1-I2V-14B-480P-Diffusers | 90.1 | 90.1 | ✓ | ✓ |
| Wan-AI/Wan2.1-I2V-14B-720P-Diffusers | 90.1 | 90.1 | ✓ | ✓ |
| Wan-AI/Wan2.1-VACE-1.3B-diffusers | 19 | 19 | ✓ | ✓ |
| Wan-AI/Wan2.1-VACE-14B-diffusers | 75.1 | 75.1 | ✓ | ✓ |
| Wan-AI/Wan2.2-T2V-A14B-Diffusers | 126.2 | 126.2 | ✓ | ✓ |
| Wan-AI/Wan2.2-I2V-A14B-Diffusers | 126.2 | 126.2 | ✓ | ✓ |
| Wan-AI/Wan2.2-Animate-14B-Diffusers | 82.2 | 82.3 | ✓ | ✓ |
| Lightricks/LTX-Video | 28.4 | 254.1 |  | ✓ |
| Lightricks/LTX-Video-0.9.7-dev | 47.6 | 47.9 |  | ✓ |
| Lightricks/LTX-Video-0.9.7-distilled | 47.6 | 47.9 |  | ✓ |
| Lightricks/LTX-Video-0.9.8-13B-distilled | 92.8 | 93.1 |  | ✓ |
| Lightricks/LTX-2 (diffusers format) | 144.6 | 314.3 |  | ✓ |
| Lightricks/LTX-2 → ltx-2-19b-dev ⁴ | 43.3 | 314.3 | ✓ |  |
| Lightricks/LTX-2 → ltx-2-19b-dev-fp8 ⁴ | 27.1 | 314.3 | ✓ |  |
| Lightricks/LTX-2 → ltx-2-19b-dev-fp4 ⁴ | 20 | 314.3 | ✓ |  |
| dg845/LTX-2.3-Diffusers | 101.2 | 101.3 | ✓ | ✓ |
| dg845/LTX-2.3-Distilled-Diffusers | 94.9 | 95 |  | ✓ |
| hunyuanvideo-community/HunyuanVideo-1.5-Diffusers-480p_t2v (+ _distilled) | 53.4 | 53.4 |  | ✓ |
| hunyuanvideo-community/HunyuanVideo-1.5-Diffusers-720p_t2v | 53.4 | 53.4 |  | ✓ |
| hunyuanvideo-community/HunyuanVideo-1.5-Diffusers-480p_i2v / 720p_i2v (+ _distilled) | 54.2 | 54.2 |  | ✓ |
| hunyuanvideo-community/HunyuanVideo-1.5-Diffusers-480p_i2v_step_distilled | 34.6 | 34.6 |  | ✓ |
| MiniMaxAI/MiniMax-H3 | 498.3 | 498.5 |  | ✓ |
| rhymes-ai/Allegro | 25.3 | 44.4 | ✓ |  |
| rhymes-ai/Allegro-T2V-40x720P | 25.3 | 25.3 | ✓ |  |
| warp-ai/wuerstchen-prior + warp-ai/wuerstchen (used as a pair) | 6.8 + 5.7 | 13.5 + 11.4 | ✓ |  |
| prs-eth/marigold-* (depth, normals, intrinsics: 8 repos) | 5.2 each | 13.8–15.5 | ✓ |  |
| cvssp/audioldm-s-full-v2 | 1.7 | 3.4 | ✓ |  |
| cvssp/audioldm-m-full | 2.6 | 5.2 | ✓ |  |
| cvssp/audioldm-l-full | 3.9 | 3.9 | ✓ |  |

1. NVFP4 저장소(repo)에는 트랜스포머(transformer)만 포함되어 있습니다. 실행하려면 FLUX.2-dev 텍스트 인코더와 VAE가 추가로 필요합니다.  
2. FLUX.2-dev-Turbo는 LoRA 모델이며, FLUX.2-dev 기반 위에서 작동합니다.  
3. SD 2 및 2.1은 여전히 DPB 목록에 남아 있지만, 현재 Hugging Face에서 두 저장소 모두 401 에러를 반환하므로(비공개/삭제된 것으로 보임) 다운로드가 불가능할 수 있습니다.  
4. DPB는 LTX-2 저장소에서 단일 체크포인트 파일 하나를 선택합니다. 표기된 크기는 해당 단일 파일만의 크기이며, 공유하여 사용하는 텍스트 인코더는 약 100 GB 정도 더 큽니다.  
   

미포함 항목: 개별적으로 선택할 수 있는 ControlNet, LoRA, 업스케일러(upscaler) 및 텍스트 인코더.

4. Publish workflow  
   1. https://docs.griptapenodes.com/en/stable/guides/publishing/#static-files-making-sure-they-get-bundled  
      2. *It exports secrets in plain text


### Nuke Gizmo / Publish Workflow {: #nuke-gizmo-publish-workflow }

https://github.com/griptape-ai/griptape-nodes-library-nuke

Nuke Start / Nuke End Flow

Start / End Flow


### 프로젝트 관리 (Managing Project) {: #managing-project }

* Setting up structures  
* Export/Import   
* Make Template workflows
