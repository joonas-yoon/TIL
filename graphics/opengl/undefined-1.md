---
description: Android에서 OpenGL ES 2.0으로 다각형을 정의하고 그려본다.
---

# 다각형 그리기

삼각형에서는 좌표들\(coords\) 배열을 9개\(3차원 점 \* 3개의 점\)만 정의했으니,  더 많은 좌표를 추가하면 다각형이 될 것이다.

하지만 그리는 것은 다르다.

OpenGL에서는 그리는 모드에 따라 각 점을 해석하는 방식이 다르다.

![&#xADF8;&#xB9BC; 1. OpenGL Drawing Primitives](../../.gitbook/assets/image%20%287%29.png)

[이전에 삼각형을 그릴 때](draw-triangle.md) 사용한 모드는 `GL_TRIANGLES` 이다.  
3개씩 묶어서 만들어지는 삼각형 면을 채우는 방식이다.

이 모드로 사각형을 그린다면, 점의 순서를 아래와 같이 바꿔야한다.

![&#xADF8;&#xB9BC; 2. &#xB450; &#xAC1C;&#xC758; &#xC0BC;&#xAC01;&#xD615;&#xC744; &#xC0AC;&#xC6A9;&#xD558;&#xC5EC; &#xC0AC;&#xAC01;&#xD615; &#xADF8;&#xB9AC;&#xAE30;](../../.gitbook/assets/image%20%286%29.png)

\(x1, y1, z1\)를 1번 정점이라고 하면, 사각형을 그리기 위해서는 \(1번-2번-3번\)의 삼각형 하나와, \(1번, 3번, 4번\)의 삼각형, 총 2개의 삼각형을 그려야한다.

여기에는 방법이 다시 2가지가 있는데, 하나는 `coords[]` 배열에 점을 3개씩 끊어서 순서대로 직접 모두 저장하거나, 점의 순서를 저장해두고 나중에 렌더링할 때 그리는 순서를 결정하는 방법이 있다.

순서대로 직접 모두 저장하는 방식은 아래와 같다.

```java
static float squareCoords[] = {
   x1, y1, z1,
   x2, y2, z2,
   x3, y3, z3, // 여기서 삼각형 하나 완성
   x1, y1, z1,
   x3, y3, z3,
   x4, y4, z4  // 두번째 삼각형 완성
};
```

점의 순서를 지정하는 코드는 아래와 같다.

```java
public class Square {

    private FloatBuffer vertexBuffer;
    private ShortBuffer drawListBuffer; // drawListBuffer에 점의 순서를 저장한다.
    
    static float squareCoords[]; // 생략
    private short drawOrder[] = { 0, 1, 2, 0, 2, 3 }; // order to draw vertices

    public Square() {
        // ...
        
        // initialize byte buffer for the draw list
        ByteBuffer dlb = ByteBuffer.allocateDirect(
                drawOrder.length * 2); // short은 2바이트라서 2를 곱한다.
        dlb.order(ByteOrder.nativeOrder());
        drawListBuffer = dlb.asShortBuffer();
        drawListBuffer.put(drawOrder);
        drawListBuffer.position(0);
    }
    
    // Renderer에서 호출하면 그린다.
    public void draw(float[] mvpMatrix) {
    
        GLES20.glUseProgram(mProgram);
        
        // ...
        
        // glDrawArrays 대신 아래와 같이 바꾼다.
        GLES20.glDrawElements(
                GLES20.GL_TRIANGLES, drawOrder.length,
                GLES20.GL_UNSIGNED_SHORT, drawListBuffer);
        
        // ...
    }
}
```

Renderer에서 호출되는 부분은 아래와 같다.

```java
@Override
public void onDrawFrame(GL10 unused) {
    // ...
    // 카메라 에 맞춰 적절히 projection된 매트릭스가 넘어간다.
    mShape.draw(mMVPMatrix);
}
```



## Links

* \(figure 1\) [https://developer.android.com/training/graphics/opengl/shapes](https://developer.android.com/training/graphics/opengl/shapes)
* \(figure 2\) [http://cse.csusb.edu/tongyu/courses/cs420/notes/drawing.php](http://cse.csusb.edu/tongyu/courses/cs420/notes/drawing.php)

