---
layout: post
title:  "How to Create a Realistic Motion Blur"
categories: SwiftUI
---

```swift
struct ContentView: View {
    @State var toggle = false
    let delay: [Double] = [
        0,
        0.01,
        0.02,
        0.03,
        0.04,
        0.05,
        0.06,
        0.07,
        0.08,
        0.09,
        0.10,
        0.11,
        0.12
    ]
    var body: some View {
        return ZStack {
            ForEach(delay, id: \.self) {
                AnyView(toggle: $toggle, delay: $0)
            }
        }
    }
}

struct AnyView: View {
    @Binding var toggle: Bool
    let delay: Double
    var body: some View {
        Rectangle()
            .frame(width: 80, height: 80)
            .cornerRadius(toggle ? 0 : 80/2)
            .foregroundColor(toggle ? .black : .pink)
            .opacity(0.1)
            .rotationEffect(Angle.degrees(toggle ? 360 : 0))
            .modifier(MyEffect(x: toggle ? -300 : 300))
            .animation(Animation.easeIn(duration: 0.5).delay(delay))
            .onTapGesture(perform: {
                toggle.toggle()
            })
    }
    
    struct MyEffect: GeometryEffect {
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