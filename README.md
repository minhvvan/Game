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
- Rendering pipeline
  ![image](https://github.com/minhvvan/Game/assets/59609086/a276f9af-b261-4b05-98c3-4be78f99dab2)

- GPU Memory Hierachy
  ![image](https://github.com/minhvvan/Game/assets/59609086/a4ed5cf3-e50e-411e-96da-e0a298a50e1f)
  
  
  Root Signature에 필요한 Resource를 담아 전달할 수 있다. Root Signature에 담겨진 내용은 실제 Data가 아니라 Data를 가리키는 포인터로 생각할 수 있다.
  Com객체를 이용해 Data를 **즉시** 복사한 후, CommandList에 존재하는 일감을 처리할 때 Data를 참조하여 사용하게 된다
  
  하지만, Data 복사는 **즉시** 일어나고 실제 참조하는 작업은 그렇지 않기 때문에 타이밍 이슈가 발생한다. -> Buffer를 여러개 두어서 관리한다.(이미 사용중인 Buffer는 변경하지 않는다)
  큰 Buffer를 할당한 후 Index로 Buffer들을 관리한다.



### Root Signature
  ![image](https://github.com/minhvvan/Game/assets/59609086/be2d5553-fa9a-406c-949b-a8c2f1eeb835)
  
  Descriptor가 많아진다면 정해진 메모리에서 모두 사용할 수 없다. 그렇기 때문에 Descriptor들을 모아 놓은 Table을 만들어 관리하면 해결할 수 있다.
  
  
### Index Buffer
  ![image](https://github.com/minhvvan/Game/assets/59609086/3ce73a14-5050-4ad2-aa9c-627abeffb7e1)
  
  만약 사각형 Mesh를 생성한다고 가정해보자.
  그렇다면 왼쪽 그림처럼 총 6개의 정점이 필요하다. 하지만, 3D Mesh를 랜더링한다고 가정해본다면 무수히 많은 사각형 혹은 삼각형이 연결된 형태일 것이다.
  그렇다면 정점이 실제로 필요한 수보다 훨씬 많이 필요해진다. 그렇다면 성능이 저하될 것이다.
  이를 해결하기 위해서는 정점을 공유하면 된다. 정점을 공유하기 위해서는 정점을 Index로 관리하는 **Index Buffer**를 이용하면 된다.


### Texture
  ![image](https://github.com/minhvvan/Game/assets/59609086/791dec2e-2b3b-4108-84f2-58bf44bd312e)

  Mesh에 Texture를 적용하기 위해서는 Texture Resource를 전달해야 한다.(register t)
  전달된 texture는 shader에서 sampling하여 Mesh에 UV를 이용하여 적용시킨다.
  
  
### Depth Stencil View
  ![image](https://github.com/minhvvan/Game/assets/59609086/5736040e-3f41-46ff-8598-f980ab458455)
  

  절두체 내 모든 Mesh를 Rendering 한다면 성능이 저하 된다. 보여지지 않는 물체는 Rendering할 이유가 없기 때문이다.  
  카메라를 기준으로 물체의 거리를 0~1 사이의 값으로 표현하여 깊이를 나타내고 Pipeline에 의해 계산된 Pixel은 depth값을 기준으로 필터링된다.
  
  (Stencil이란 정점에 추가적인 정보를 같이 넘겨 나중에 정보를 이용하여 다른 작업을 수행할 수 있게 하는 기법이다.)
  
  
  
### 정점 변환
 - 물체가 카메라에의해 Rendering 되려면 여러차례의 좌표계 변환이 이루어져야 한다.
 - 변환의 순서는 Local → World → View → Clip → Screen 순이다.
  ![image](https://github.com/minhvvan/Game/assets/59609086/4f4e7385-6656-4d4b-b05d-cef48b2e2a57)

  - Local Space: 물체의 고유한 좌표 공간을 말한다. (물체가 기준이 됨)
  - World Space: 물체가 표현되는 하나의 공간을 표현하기 위한 좌표 공간을 말한다.
  - View Space: World Space에 표현된 물체들을 카메라의 시선에서 표현한 좌표 공간을 말한다. (카메라의 위치에 따라 상대적으로 표현)
  - Clip Space: View Space로 변환된 좌표계의 원근을 표현하기 위해 정의된 절두체를 변형시킨 좌표 공간을 말한다.
  - Screen Space: 실제 화면에 보여지는 좌표 공간을 말한다.


### Deferred Rendering
  ![image](https://github.com/minhvvan/Game/assets/59609086/92799e4f-9aaf-4732-8b57-02899daf1822)
  Forward Rendering
  
  ![image](https://github.com/minhvvan/Game/assets/59609086/26c88c0d-194e-415c-9f20-4f2c5100798c)
  Deferred Rendering
  
  기존 방식인 Forward Rendering은 모든 계산을 마친 후 Final Render를 진행한다. 따라서, 라이팅 계산수가 Mesh의 수에 비례하게 되고, 빛의 영향을 받지 않은 Mesh에 대해서도 연산을 시행하게 되어 불필요한 연산이 행해지게 되는 단점이 있다. 이를 보안하기 위한 방법이 Deferred Rendering이다.
  Deferred Rendering은 쉽게 말해 중간 단계(Diffuse, Screen Space Normal, Specular, Smoothness, Screen Space Depth)를 저장하고 있다 Final Render에서 조립하는 방법이다.
  
  
### Particle System & Instancing
  VFX나 어떠한 목적을 위한 Particle을 생성할 때, 정점(Vertex)을 모두 생성하면 성능에 문제가 생길 수 있다.
  이러한 문제를 Particle System을 이용하여 점을 생성한 후 특정 texture를 Instacing 한다면 성능상에 이점을 얻을 수 있다.

### Shadow Mapping
  그림자를 표현하기 위해서는 광원을 기준으로 Depth를 계산하여 Shadow Texture를 생성하면 쉽게 구현할 수 있다. 
  광원에서 가장 가까운 물체들의 위치(Position)을 알고 있다면 해당하는 부분에 Shadow를 적용하면 된다.

### Animation
  ![dx12](https://github.com/minhvvan/Game/assets/59609086/a293a3ad-2e22-4a4a-9597-4a0226e2e8a7)
  ![image](https://github.com/minhvvan/Game/assets/59609086/9cd4f0b4-461c-401e-9d41-56551d2d2d8e)
  
  Skeletion을 기준으로 animation을 실행하고 skin을 붙이는 구조로 실행된다.  
  Skeletion은 Hierarchy한 구조로 이루어져 있으며, 여러번의 좌표 변환이 실행되며 움직인다.
  
