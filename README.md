# DirectX 12
> DX12를 이용하여 랜더링 파이프라인을 학습한다. 


## Tech Stack
<p>
<img src="https://img.shields.io/badge/c++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white">
</ p>

## 구현 목록
### 장치 초기화
- Device: Com객체(DX의 프로그래밍 언어 독립성과 하위 호환성을 가능하게 하는 기술)를 갖고 전체적인 관리를 하는 class
- CommandQueue: 일감을 순서대로 저장해 놓은 Queue, 나중에 한번에 요청한다.
- fence: 일감을 보호하는 역할(결과물을 기다리기위한)
- SwapChain: 화면을 Dubble Buffering으로 처리했기 때문에 Render되고 있는 화면과 다음 Frame을 준비하는 화면을 swap하는 class
- DescriptorHeap:DX12에서 추가된 개념(이전 Ver에서는 View), Resource를 전달할 때 Resource에 대한 설명과 용도를 서술하는 class


### Mesh Rendering
- RootSignature: CPU와 GPU간 메모리사용에 대한 규칙을 정의하는 구조체
-  Mesh를 Render하기 위해서는 정점간 연결이 필요하다. 각 정점은 위치와 색 등 다양한 정보를 담을 수 있음
이러한 정보들을 바로 GPU 메모리에 접근하여 저장할 수 없기 때문에 Com객체를 이용하여 간접적으로 복사함
- Shader: GPU에서 받아온 Data로 무엇을 어떻게 해야할지 정의한 class, Vertext Shader, Pixel Shader등 다양한 Shader 종류가 있음 (hlsl이라는 언어를 사용함)


### Constant Buffer
-


### 애니메이션
![dx12](https://github.com/minhvvan/Game/assets/59609086/a293a3ad-2e22-4a4a-9597-4a0226e2e8a7)
#### 애니메이션 구현 원리
