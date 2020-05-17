---
layout: post
title: "Implementing Material Design themes using SwiftUI - Part 2"
# image: /img/consistency.png
subtitle: 
# bigimg: /img/path.jpg
published: true
tags: [themes, dependency inversion, generics, swiftui]
excerpt: In this tutorial, we define abstractions and invert the dependencies in our container view so we can inject our reusable SwiftUI componenets.
comments_id: 21
---

In the [previous tutorial](/implementing-material-design-themes-using-swiftui), we learned how to create reusable components, applied theme to them and then implemented a fully themed screen using SwiftUI.
In this part, we will truly create a generic Container view and inject abstract components.

This will allow our screen to consume the components and guide their layout, but not how those components are rendered to the screen. 

In order to that, let's start by identifying abstractions that we need for the ThemeExampleDemo view.

<center> 
<img src="/img/themeExample1.jpg" alt="Mobile app layout with a title, caption, image and content." />
</center>

So we created 3 reusable views in our previous tutorial namely:
- HeadlineWithCaption
- TitleWithText &
- Action Button

In order to create abstractions for these components, we will make ThemeExampleDemo generic, and add the below private properties in the view.


```
struct ThemeExampleDemo<T: View, U: View, V: View>: View {

// For header, that takes in a title & a caption
private let headerWithCaptionViewProvider: (Binding<String>, Binding<String>) -> T

// For the main content view that takes in a title & paragraph content
private let contentViewProvider: (Binding<String>, Binding<String>) -> U

// The 3 action buttons that our view relies on
private let primaryButton: V
private let secondaryButton: V
private let tertiaryButton: V
...

```

> Here we are inverting the dependencies and making our higher level component i.e. ThemeExampleDemo rely on an abstraction (a function in this case) that could be implemented by a lower level component. 

Note that you can also define explicit protocols instead of functions for these abstractions.

Time to write the initializer for our view.

```
init(
    headerWithCaptionViewProvider: @escaping (Binding<String>, Binding<String>) -> T,
    contentViewProvider: @escaping (Binding<String>, Binding<String>) -> U,
    primaryButton: (String) -> V,
    secondaryButton: (String) -> V,
    tertiaryButton: (String) -> V
) {
    self.headerWithCaptionViewProvider = headerWithCaptionViewProvider
    self.contentViewProvider = contentViewProvider
    self.primaryButton = primaryButton("Red Theme")
    self.secondaryButton = secondaryButton("White Theme")
    self.tertiaryButton = tertiaryButton("Switch Dark Mode")
}
```

Sweet! All we are left to do is update the `var body` to actually use these abstractions.

```
var body: some View {
    return ZStack {
        VStack {
            headerWithCaptionViewProvider($headlineTitle, $headlineCaption)
            Spacer()
            Image(uiImage: UIImage(named: "image")!)
                .resizable()
                .clipShape(Circle())
                .frame(width: 200, height: 200)
                .shadow(color: theme.colorPalette.secondary, radius: 10.0, x: 0, y: 0)

            Spacer()
            HStack(spacing: 16.0) {
                Spacer()
                contentViewProvider($contentTitle, $contentText)
                Spacer()
            }
            HStack(spacing: 16.0) {
                primaryButton
                secondaryButton
            }.padding(.top, 24.0)
            tertiaryButton
                .padding(.top, 12.0)
            Spacer()
        }.padding(.top, 32.0)
    }
}
```

That's it. [Here is the link](https://github.com/developerdialogs/material-ui-theme-ios-example/tree/master) to the completed example playground that you can *play* with.

Cheers!