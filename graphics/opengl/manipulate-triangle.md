---
description: Android에서 OpenGL ES 2.0으로 객체를 터치한 만큼 옮겨본다.
---

# 삼각형 움직이기

[이전에 그렸던 삼각형 객체](draw-triangle.md)를 터치(Touch)한 만큼 옮겨볼 것이다.

{% hint style="warning" %}
[공식 문서와 예제](https://developer.android.com/training/graphics/opengl/motion)는 삼각형을 회전시키지만, 터치 움직임과 너무 맞지 않는 동작이다.

나는 삼각형을 **움직이도록** 수정하였다.
{% endhint %}

![\[그림 1\] Translate Triangle](../../.gitbook/assets/android-opengl-translate.gif)

기존 코드에서 변경되는 부분은 거의 없다. 렌더러(Renderder)에서 위치를 이동시키는 행렬을 하나 더 추가하고 그것을 카메라 뷰와 합치면 된다.

먼저 터치 이벤트를 등록해야한다.

```java
public class MyGLSurfaceView extends GLSurfaceView {
    ...

    private float mPreviousX;
    private float mPreviousY;

    @Override
    public boolean onTouchEvent(MotionEvent e) {
        // MotionEvent reports input details from the touch screen
        // and other input controls. In this case, you are only
        // interested in events where the touch position changed.
        float x = e.getX();
        float y = e.getY();
        switch (e.getAction()) {
            case MotionEvent.ACTION_MOVE:
                float dx = mPreviousX - x;
                float dy = mPreviousY - y;
                mRenderer.translate(dx, dy, 0f); // translate
                requestRender();
        }
        mPreviousX = x;
        mPreviousY = y;
        return true;
    }
}
```

터치 이벤트가 발생할 때마다 이동한 만큼 변위값을 렌더러에 넘겨준다.

```java
public class MyGLRenderer implements GLSurfaceView.Renderer {
    ...
    private final float[] mTranslateMatrix = new float[16];
    private int screenHeight;
    private int screenWidth;

    @Override
    public void onSurfaceCreated(GL10 unused, EGLConfig config) {
        ...
        Matrix.setIdentityM(mTranslateMatrix, 0);
    }

    @Override
    public void onSurfaceChanged(GL10 unused, int width, int height) {
        this.screenWidth = width;
        this.screenHeight = height;
        ...
    }

    @Override
    public void onDrawFrame(GL10 unused) {
        float[] scratch = new float[16];
        
        ...
        
        // Combine the translated matrix with the projection and camera view
        Matrix.multiplyMM(scratch, 0, mMVPMatrix, 0, mTranslateMatrix, 0);

        // Draw triangle
        mTriangle.draw(scratch);
    }

    public void translate(float dx, float dy, float dz) {
        Matrix.translateM(mTranslateMatrix, 0,
            dx * 2f / screenHeight,
            dy * 2f / screenHeight,
            dz * 2f / screenHeight);
    }
}
```

`...` 부분은 기존의 코드와 동일하다.

터치의 변위값, 즉 드래그한 만큼 `mTranslateMatrix` 에 반영한다. 그리고 `onDrawFrame()` 에서, 이동을 위해 추가한 행렬 `mTranslateMatrix` 도 결합시켜 그리도록 한다.

주의할 점은,  `mTranslateMatrix` 의 최초 상태를 단위 행렬(Identity Matrix)로 만들어주고 시작해야한다.

추가로 살필 수 있는 부분으로는, `dx * 2f / screenHeight` 라는 수식이다.&#x20;

터치의 변위값은 화면의 좌표계를 사용했다. 화면 비율에 맡게 가로/세로값으로 좌표계를 수정했었으니 그에 맞게 다시 변환해준 것이다.&#x20;

## Code

[https://github.com/joonas-yoon/android-opengl-example/tree/924058f8eb8fbbf566ffccc8e72c0cd308f800d6](https://github.com/joonas-yoon/android-opengl-example/tree/924058f8eb8fbbf566ffccc8e72c0cd308f800d6)

## Links

* [https://developer.android.com/training/graphics/opengl/motion](https://developer.android.com/training/graphics/opengl/motion)
* [https://android.googlesource.com/platform/development/.../example/android/opengl](https://android.googlesource.com/platform/development/+/master/samples/OpenGL/HelloOpenGLES20/src/com/example/android/opengl)
