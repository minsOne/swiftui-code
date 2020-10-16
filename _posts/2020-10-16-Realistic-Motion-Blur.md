---
layout: post
title:  "Realistic Motion Blur"
categories: "SwiftUI"
video: "motionblur.mp4"
video_thumbnail: "motionblur-thumbnail.mp4"
---

```swift
import SwiftUI

struct ContentView: View {
  @State private var isAnimate = false
  
  let delay: [Double] = [
    0, 0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.07, 0.08, 0.09, 0.10, 0.11, 0.12
  ]
  
  var body: some View {
    return VStack {
      ZStack {
        ForEach(delay, id: \.self) {
          BlurView(isAnimate: $isAnimate, delay: $0)
        }
      }.frame(width: 300, height: 300, alignment: .center)
    }
  }
}

struct BlurView: View {
  @Binding var isAnimate: Bool
  let delay: Double
  
  var body: some View {
    GeometryReader(content: view)
  }
  
  func view(_ geometry: GeometryProxy) -> some View {
    let size = geometry.size
    let animateViewSize = size.height/4
    return Rectangle()
      .frame(width: animateViewSize, height: animateViewSize)
      .cornerRadius(isAnimate ? 0 : size.height/4)
      .foregroundColor(isAnimate ? .black : .pink)
      .opacity(0.1)
      .rotationEffect(Angle.degrees(isAnimate ? 360 : 0))
      .modifier(Offset(x: !isAnimate ? 0 : size.width*3/4))
      .animation(Animation.easeIn(duration: 0.5).delay(delay))
      .offset(x: 0, y: (size.height-animateViewSize)/2)
      .onTapGesture(perform: {
        isAnimate.toggle()
      })
  }
  
  struct Offset: GeometryEffect {
    var x: CGFloat = 0
    
    var animatableData: CGFloat {
      get { x }
      set { x = newValue }
    }
    
    func effectValue(size: CGSize) -> ProjectionTransform {
      return ProjectionTransform(CGAffineTransform(translationX: x, y: 0))
    }
  }
}
```

## Reference
* [How to Create a Realistic Motion Blur with CSS Transitions](https://css-tricks.com/how-to-create-a-realistic-motion-blur-with-css-transitions/)