---
layout: post
title: "Implementing Material Design themes using SwiftUI"
# image: /img/consistency.png
subtitle: 
# bigimg: /img/path.jpg
published: true
tags: [themes, material design, swiftui]
excerpt: Implementing themes help present a consistent design scheme and good for the maintainability of presentation layer for your app. Let's see at an approach to implementing themes in this developer dialogue.
---

In this dialogue, we will learn one approach of implementing themes in your app. As an added bonus, we will create two variations of it and switch between them at runtime without having to restart the app!

We will be using the power of composition and declarative programming in SwiftUI to achieve this.

- SwiftUI: [https://developer.apple.com/videos/play/wwdc2019/216/](https://developer.apple.com/videos/play/wwdc2019/216/){:target="_blank"}
- Material Design: [https://material.io/design/material-theming/implementing-your-theme.html](https://material.io/design/material-theming/implementing-your-theme.html){:target="_blank"}

## Layout
This is the layout we wish to achieve.
<center> 
<img src="/img/themeExample1.png" />
</center>

We will start by identifying and decomposing different sections in this layout. This will let you define reusable components that can be used across different screens in your app hence achieving a consistent design.

In this layout, you can see, we have 4 distinguished sections:
1. Headline
1. Image
1. Content
1. Action Buttons

<center> 
<img src="/img/themeExampleComponents.png" />
</center>

We will start by implementing these reusable components & then later combine them in our final container view.

---


**HeadlineWithCaption** - Basic component that contains a headline along with a caption.
```
struct HeadlineWithCaption: View {
    @Binding var title: String
    @Binding var caption: String
    
    var body: some View {
        VStack {
            Text(title)
            Text(caption)
        }
    }
}
```

**TitleWithText** - Basic component for content to display text along with a title.

```
struct TitleWithText: View {
    @Binding var title: String
    @Binding var text: String
    
    var body: some View {
        VStack {
            Text(title)
            Text(text)
                .multilineTextAlignment(.center)
        }
    }
}
```

**Action Button** - Reusable action button. 
We are not going to bind the title for the button, since it is assumed we wish to keep them constant.

This component also does not have an action, since that is beyond the scope, but you can just as easily pass it as a parameter.
```
struct ActionButton: View {
    let title: String

    var body: some View {
        Button(action: {}) {
            Text(title)
                .frame(width: 150, height: 50.0)
        }
        .cornerRadius(12.0)
    }
}
```
---

#### and finally.... the Container View

Don't forget to update `UIImage(named: "image")!` with the correct asset you wish to use.

```
struct ThemeExampleDemo: View {    
    @State private var headlineTitle = "Theme Example"
    @State private var headlineCaption = "Let's get the themes changing!"
    @State private var contentTitle = "What are Themes"
    @State private var contentText = "Themes let you apply a consistent design scheme to your app. It allows you to customize all design aspects of your project."
    
    var body: some View {
        ZStack {
            VStack {
                HeadlineWithCaption(title: $headlineTitle, caption: $headlineCaption)
                Spacer()
                Image(uiImage: UIImage(named: "image")!)
                    .resizable()
                    .clipShape(Circle())
                    .frame(width: 200, height: 200)
                Spacer()
                HStack(spacing: 16.0) {
                    Spacer()
                    TitleWithText(title: $contentTitle, text: $contentText)
                    Spacer()
                }
                
                HStack(spacing: 16.0) {
                    ActionButton(title: "Primary")
                    ActionButton(title: "Secondary")
                }.padding(.top, 24.0)
                
                ActionButton(title: "Link")
                    .padding(.top, 12.0)
                
                Spacer()
            }.padding(.top, 32.0)
        }
    }
}
```

After you are done with above, you should have something similar to below:

<center> 
<img src="/img/themeExample3.png" />
</center>

Easy Peasy!

## Theming

Now that we have our components figured out. We are ready to implement themes. Let's get started!

Start off by creating a `struct Theme` that defines the semantic layer of our theme. Since we are basing off our theme on Material Design, we will define the Color & Typography of our theme as below:

```
struct Theme {
    private(set) var colorPrimary: Color = .black
    private(set) var colorSecondary: Color = .gray
    private(set) var colorPrimaryVariant: Color = .black
    private(set) var colorSecondaryVariant: Color = .gray
    private(set) var colorBackground: Color = .white
    private(set) var colorSurface: Color = .white
    private(set) var colorError: Color = .white
    
    private(set) var colorOnPrimary: Color = .white
    private(set) var colorOnSecondary: Color = .white
    private(set) var colorOnBackground: Color = .black
    private(set) var colorOnSurface: Color = .black
    private(set) var colorOnError: Color = .red
    
    private(set) var typographyH1: Font = Font.system(size: 40).weight(.bold)
    private(set) var typographyH2: Font = Font.system(size: 36).weight(.bold)
    private(set) var typographyH3: Font = Font.system(size: 32).weight(.bold)
    private(set) var typographyH4: Font = Font.system(size: 24).weight(.semibold)
    private(set) var typographyH5: Font = Font.system(size: 20).weight(.semibold)
    private(set) var typographyH6: Font = Font.system(size: 18).weight(.semibold)
    
    private(set) var typographySubtitle1: Font = Font.system(size: 16)
    private(set) var typographySubtitle2: Font = Font.system(size: 14)
    
    private(set) var typographyBody1: Font = Font.system(size: 16)
    private(set) var typographyBody2: Font = Font.system(size: 14)
    
    private(set) var typographyButton: Font = Font.system(size: 14)
    
    private(set) var typographyCaption: Font = Font.system(size: 12)
    private(set) var typographyOverline: Font = Font.system(size: 14)
}
```

As you can see we define the general typography & color behaviors to use within our app. Of course you can extend it with more distinguished and custom attributes. For example `colorSuccess` & `colorOnSuccess`.


We also provide default values for our theme, so that we can start using it immediately.

### Providing theme to our components
A theme, when talking from the perspective of your app, is a global concept. All the UI components must inherently depend on your theme struct and SwiftUI provides us just the right tool to acheive this.

**EnvironmentObject** - [https://developer.apple.com/documentation/swiftui/environmentobject](https://developer.apple.com/documentation/swiftui/environmentobject){:target="_blank"}

We will create an environment object `class AppTheme`:
```
class AppTheme: ObservableObject {
    @Published var theme: Theme
    
    init(theme: Theme) {
        self.theme = theme
    }
}
```

Next, we will update our reusable components from previous section and apply styles to them.

In order to use the environment object, you need to add:
```
@EnvironmentObject var env: AppTheme
```

into the components.

Here is a basic sample of styles applied to our components:

```
struct HeadlineWithCaption: View {
    @Binding var title: String
    @Binding var caption: String
    @EnvironmentObject var env: AppTheme
    
    var body: some View {
        VStack {
            Text(title)
                .font(env.theme.typographyH1)
                .foregroundColor(env.theme.colorOnBackground)
            Text(caption)
                .font(env.theme.typographySubtitle1)
                .foregroundColor(env.theme.colorOnBackground)
        }
    }
}

struct TitleWithText: View {
    @Binding var title: String
    @Binding var text: String
    @EnvironmentObject var env: AppTheme
    
    var body: some View {
        VStack {
            Text(title)
                .font(env.theme.typographyH5)
                .foregroundColor(env.theme.colorOnBackground)
            Text(text)
                .font(env.theme.typographyBody1)
                .foregroundColor(env.theme.colorOnBackground)
                .multilineTextAlignment(.center)
        }
    }
}

enum ButtonType {
    case primary, secondary, tertiary
}

struct ActionButton: View {
    private let title: String
    private let type: ButtonType
    @EnvironmentObject var env: AppTheme
    private var foregroundColor: Color {
        switch type {
        case .primary:
            return env.theme.colorOnPrimary
        case .secondary:
            return env.theme.colorOnSecondary
        case .tertiary:
            return env.theme.colorOnSurface
        }
    }
    
    private var backgroundColor: Color {
        switch type {
        case .primary:
            return env.theme.colorPrimary
        case .secondary:
            return env.theme.colorSecondary
        case .tertiary:
            return env.theme.colorSurface
        }
    }
    init(title: String, type: ButtonType) {
        self.title = title
        self.type = type
    }
    var body: some View {
        Button(action: { }) {
            Text(title)
                .font(.system(.headline))
                .fontWeight(.bold)
                .foregroundColor(foregroundColor)
                .frame(width: 150, height: 50.0)
        }
        .background(backgroundColor)
        .cornerRadius(12.0)
    }
}
```

In the above example, I have added an `enum ButtonType` to account for different types of button that we require.

Next, go ahead and update `ThemeExampleDemo` view to use the buttons correctly. ButtonType should be `.primary` for "Primary", `.secondary` for "Secondary" and `.tertiary` for "Link".

Let's also apply shadow to our image as below:

```
Image(uiImage: UIImage(named: "image")!)
                    .resizable()
                    .clipShape(Circle())
                    .frame(width: 200, height: 200)
                    .shadow(color: env.theme.colorSecondary, radius: 10.0, x: 0, y: 0)
```

and lastly creating and passing the environment object to our view:

```
let blackTheme = Theme()
var env = AppTheme(theme: blackTheme)

ThemeExampleDemo().environmentObject(env)
```

### Switching between themes

Switching between themes is as easy as it can be. All we have to do is instantiate the new theme and update the environment variable.

```
let redTheme = Theme(
    colorPrimary: .red, 
    colorSecondary: .red, 
    colorPrimaryVariant: .red, 
    colorSecondaryVariant: .red, 
    colorBackground: .red, 
    colorOnSecondary: .black, 
    colorOnBackground: .red, 
    colorOnSurface: .red, 
    typographyH1: Font.system(size: 40).italic(),
    typographyH2: Font.system(size: 36).italic(),
    typographyH3: Font.system(size: 32).italic(),
    typographyH4: Font.system(size: 24).italic(),
    typographyH5: Font.system(size: 20).italic(),
    typographyH6: Font.system(size: 18).italic())

env.theme = redTheme
```

## Finally...

<center> 
<img src="/img/themeDemo.gif" />
</center>


In the next dialogue, we will take this a step further and instead of instantiating the reusable components directly in our view and violating the SOLID principles of programming, we will make our container view generic and inject them instead. That will help us achieve true composability and also increase the testability of our views.