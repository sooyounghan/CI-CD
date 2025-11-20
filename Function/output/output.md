-----
### output
-----
1. 한 job에서 생성된 데이터를 동일한 job의 step, 다른 job들과 데이터를 공유 (즉, 여러 step과 job 간에 데이터를 손 쉽게 전달 가능)
2. artifact과 output
   - artifact : 파일 또는 파일 모음으로 데이터 공유 가능
   - output : 단순한 값을 전달할 때 사용하며, key-value 형태로 저장 (```echo "{key}={value}" >> $GITHUB_OUTPUT : echo name=github-actions >>  $GITHUB_OUTPUT```)
3. 같은 job에서 output를 사용하려면, output이 정의된 step의 고유 id를 사용해야 함
   - push Event가 발생할 때, 트리거가 되는 워크플로우로, 첫 번쨰 스텝 echo output에서 test=hello output 설정하는데, id를 check-output 사용
<div align="center">
<img src="https://github.com/user-attachments/assets/156ce70c-a300-4977-86dd-e9a0b24e9d88">
</div>

   - 아래 그림을 보면, 첫 번째 스텝과 두 번쨰 스텝의 output 키가 test로 동일하므로, id와 같은 구분자가 없으면 동일한 output key일 때, 구분이 되지 않으므로, Github Actions에서는 각 step에서는 고유한 id를 사용해야 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/5b8e2477-b324-4aff-a262-1d7ef5e8ee95">
</div>

   - 출력 포맷 : steps에서 생성한 check-output id에 대해 접근하고, output의 test 키에 접근해 값을 출력
<div align="center">
<img src="https://github.com/user-attachments/assets/48bface3-976a-4a2a-9ee0-cfac308bf8b8">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/68a8901e-6654-42d1-b433-6c3539c77b51">
</div>

4. 다른 job에서 output을 사용하려면 needs, job-level outputs 사용
   - create-output이라는 job-level로 output 정의 (job level에서의 output도 key-value 형태로 데이터 저장)
   - job의 step-level에서 생성한 output을 job-level에서도 사용 가능
   - job-level에서 output 설정을 완료하면, 종속성이 있는 job은 해당 output을 사용할 수 있음 : needs를 이용해 [create-output]이 완료된 이후 실행 (needs.create-output.outputs.test로 접근해 해당 output에 접근 가능)
<div align="center">
<img src="https://github.com/user-attachments/assets/348f4df5-7e95-4ba4-9103-56c628e32881">
</div>

5. 실습
```yaml
name: output
on: push

jobs:
  create-output:
    runs-on: ubuntu-latest
    outputs:
      test: ${{ steps.check-output.outputs.test }}
    steps:
      - name: echo output
        id: check-output
        run: |
          echo "test=hello" >> $GITHUB_OUTPUT
      - name: check output
        run: |
          echo ${{ steps.check-output.outputs.test }}

  get-output:
    needs: [create-output]
    runs-on: ubuntu-latest
    steps:
      - name: get output
        run: echo ${{ needs.create-output.outputs.test }}
```

<div align="center">
<img src="https://github.com/user-attachments/assets/72f6aec3-5e88-4440-8df7-088e2f8f3424">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a3d6d1d8-9617-4f2e-a4fb-ea2fbdacd453">
</div>
