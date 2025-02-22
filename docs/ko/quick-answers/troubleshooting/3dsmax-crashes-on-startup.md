---
layout: default
title: Shotgun 툴킷 사용 시 3ds Max를 시작하면 충돌 오류가 발생하는 이유는 무엇입니까?
pagename: 3dsmax-crashes-on-startup
lang: ko
---

# Shotgun 툴킷 사용 시 3ds Max를 시작하면 충돌 오류가 발생하는 이유는 무엇입니까?

Shotgun 데스크톱이나 Shotgun 웹 사이트에서 3ds Max를 실행하면 하얀색 대화상자가 움직이지 않는 상태로 3ds Max가 멈추거나 다음 메시지가 나타날 수 있습니다.

    Microsoft Visual C++ 런타임 라이브러리(응답 없음)
    런타임 오류!
    프로그램: C:\Program Files\Autodesk\3ds Max 2016\3dsmax.exe
    R6034
    응용프로그램에서 C 런타임 라이브러리를 잘못 로드하려는 시도가 있었습니다.
    자세한 내용은 응용프로그램 지원 팀에 문의하십시오.

이는 보통 경로의 `msvcr90.dll` 버전이 3ds Max와 번들로 제공되는 Python 버전과 충돌하기 때문입니다. 

## 솔루션

먼저, 파이프라인 구성의 `config/hooks` 폴더로 이동하여 `before_app_launch.py` 파일을 생성합니다. 이 파일에서 다음을 붙여 넣습니다.

```python
"""
Before App Launch Hook
This hook is executed prior to application launch and is useful if you need
to set environment variables or run scripts as part of the app initialization.
"""
import os
import tank

class BeforeAppLaunch(tank.get_hook_baseclass()):
    """
    Hook to set up the system prior to app launch.
    """
    def execute(self, **kwargs):
        """
        The execute functon of the hook will be called to start the required application
        """
        env_path = os.environ["PATH"]
        paths = env_path.split(os.path.pathsep)
        # Remove folders which have msvcr90.dll from the PATH
        paths = [path for path in paths if "msvcr90.dll" not in map(
            str.lower, os.listdir(path))
        ]
        env_path = os.path.pathsep.join(paths)
        os.environ["PATH"] = env_path
```

이제 파일을 저장합니다.

그런 다음, 파이프라인 구성에서 `config/env/includes/app_launchers.yml`을 열고 `launch_3dsmax` 항목을 찾습니다. `hook_before_app_launch: default`를 `hook_before_app_launch: '{config}/before_app_launch.py'`로 바꿔야 합니다.

이제 Shotgun 및 Shotgun 데스크톱에서 3ds Max가 올바로 실행될 것입니다. 그래도 문제가 발생하면 support@shotgunsoftware.com으로 문의하시기 바랍니다.
