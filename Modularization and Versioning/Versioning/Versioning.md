-----
### 버저닝(Versioning)이 필요한 이유
-----
1. 모듈화가 반영된 상태라고 가정
   - A Repository의 이미지 빌드에만 먼저 변경사항을 적용
   - 하지만, 필요에 따라 특정 Repository에 먼저 변경사항을 적용하고, 안정적이면 다른 레포로 확장해야 할 때가 존재
   - 하지만 Module Repository의 image-build를 수정하면 A, B, C Repository 모두 영향을 줄 수 있는데, 이 문제를 해결하기 위해 필요한 것이 Versioning
   - A, B, C Repository에서 Module Repository에 Version 1을 사용
<div align="center">
<img src="https://github.com/user-attachments/assets/552acefa-0826-408e-a30b-d73e56643594">
</div>

   - 이 때, 다른 Repository에 영향 없이 A Repository에만 변경 사항을 적용하고 싶다면, Module Repository에 Version 2를 만들고, A Repository에서 이를 참조
   - 이렇게 함으로써, B, C Repository는 기존 버전에 영향을 받지 않고, 유지되는 반면, A Repository만 새로운 변경사항을 적용받게 됨
<div align="center">
<img src="https://github.com/user-attachments/assets/85579b29-1434-448d-9afb-e3ec910e2d7d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ec0f25cf-5036-4781-b905-e47fe3c692da">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/67971da6-68d3-4804-8ced-751270be4ca8">
</div>

   - A Repository에만 적용했던 변경사항을 B, C Repository에도 적용하길 원한다면, Version 2를 참조하도록 하면 됨
<div align="center">
<img src="https://github.com/user-attachments/assets/87866849-1a66-4e27-b9ba-73b6785b9202">
</div>
