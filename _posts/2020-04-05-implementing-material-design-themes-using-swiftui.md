---
layout: post
title: "Implementing Material Design themes using SwiftUI"
# image: /img/consistency.png
subtitle: 
# bigimg: /img/path.jpg
published: true
tags: [themes, material design, swiftui]
excerpt: Implementing themes help present a consistent design scheme and good for the maintainability of presentation layer for your app. Let's see at an approach to implementing themes in this tutorial.
comments_id: 15
---

In this dialogue, we will learn one approach of implementing themes in your app. As an added bonus, we will create two variations of it and switch between them at runtime without having to restart the app!

We will be using the power of composition and declarative programming in SwiftUI to achieve this.

- SwiftUI: [https://developer.apple.com/videos/play/wwdc2019/216/](https://developer.apple.com/videos/play/wwdc2019/216/){:target="_blank"}
- Material Design: [https://material.io/design/material-theming/implementing-your-theme.html](https://material.io/design/material-theming/implementing-your-theme.html){:target="_blank"}

You can view the entire playground source code [here](https://github.com/developerdialogs/material-ui-theme-ios-example){:target="_blank"}

## Layout
This is the layout we wish to achieve.
<center> 
<img src="/img/themeExample1.jpg" alt="Mobile app layout with a title, caption, image and content." />
</center>

We will start by identifying and decomposing different sections in this layout. This will let you define reusable components that can be used across different screens in your app hence achieving a consistent design.

In this layout, you can see, we have 4 distinguished sections:
1. Headline
1. Image
1. Content
1. Action Buttons

<center> 
<img src="/img/themeExampleComponents.jpg" alt="Different components in the previous layout." />
</center>

We will start by implementing these reusable components & then later combine them in our final container view.

Let's open Xcode & create a new playground named **ThemeExample**. Open the Project Navigator and under `Sources` create a new folder `UIComponents`. Once done, create the below files in it.


> Playground Tip: Since we are creating these files under `Sources`, these will be available as a framework to our main playground file. Hence we need to make our `classes` & `structs` *public* and `inits` *explicit*.


---

**HeadlineWithCaption.swift** - Basic component that contains a headline along with a caption.
```
public struct HeadlineWithCaption: View {
    @Binding var title: String
    @Binding var caption: String

    public init(title: Binding<String>, caption: Binding<String>) {
        _title = title
        _caption = caption
    }
    
    public var body: some View {
        VStack {
            Text(title)
            Text(caption)
        }
    }
}
```

**TitleWithText.swift** - Basic component for content to display text along with a title.

```
struct TitleWithText: View {
    @Binding var title: String
    @Binding var text: String

    public init(title: Binding<String>, text: Binding<String>) {
        _title = title
        _text = text
    }
    
    var body: some View {
        VStack {
            Text(title)
            Text(text)
                .multilineTextAlignment(.center)
        }
    }
}
```

**ActionButton.swift** - Reusable action button. 
We are not going to bind the title for the button, since it is assumed we wish to keep them constant.

This component also does not have an action, since that is beyond the scope, but you can just as easily set it.
```
struct ActionButton: View {
    let title: String

    public init(title: String) {
        self.title = title
    }

    var body: some View {
        Button(action: {}) {
            Text(title)
                .frame(width: 150, height: 50.0)
        }
        .cornerRadius(12.0)
    }
}
```

After this step, your playground structure should look something like this:

<center> 
<img src="/img/themeExampleProjectStructure1.jpg" alt="Project structure with UIComponents subfolder under Sources containing ActionButton.swift, HeadlineWithCaption.swift and TitleWithText.swift"/>
</center>

---

#### and finally.... the Container View

Open ThemeExample and add the view `ThemeExampleDemo` as below.
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

After you are done with above, check out the live preview. You should have your view laid out like this:

<center> 
<img src="/img/themeExample3.jpg" alt="App layout without any applied styles"/>
</center>

Easy Peasy!

## Theming

Now that we have our components figured out. We are ready to implement themes. Let's get started!

Under Sources, add a new folder "Themes". We will use it to define our theme structure.
Create a new file, `MaterialTheme.swift` and create a `class MaterialTheme` that will define the semantic layer of our theme. Since we are basing off our theme on Material Design, we will define the Color & Typography of our theme as below.

> The reason we define MaterialTheme as a `class` (and not `struct`) is because we will use it as an Environment Object and need to conform to `ObservableObject` protocol. More on it below.


```
public class MaterialTheme {

    public struct ColorPalette {
        public private(set) var primary: Color
        public private(set) var secondary: Color
        public private(set) var primaryVariant: Color
        ...
        ...

        public private(set) var onPrimary: Color
        public private(set) var onSecondary: Color
        ...


        public init(
            primary: Color = .black,
            secondary: Color = .gray ...) {
            self.primary = primary
            self.secondary = secondary
            ...
        }
    }

    public struct Typography {
        public private(set) var h1: Font
        public private(set) var h2: Font
        ...
        ...
        public private(set) var button: Font

        public init(
            h1: Font = Font.system(size: 40).weight(.bold),
            h2: Font = Font.system(size: 36).weight(.bold) ...) {
            self.h1 = h1
            self.h2 = h2
            ...
            ...
        }
    }

    public var colorPalette: ColorPalette
    public var typography: Typography

    public init(colorPalette: ColorPalette = ColorPalette(), typography: Typography = Typography()) {
        self.colorPalette = colorPalette
        self.typography = typography
    }
}
```

As you can see we define the general typography & color behaviors to use within our app. Of course you can extend it with more distinguished and custom attributes. For example `colorSuccess` & `colorOnSuccess`.


We also provide default values, so that we can start using it immediately.

### Providing theme to our components
A theme, when talking from the perspective of your app, is a global concept. All the UI components inherently depend on your theme for styling and SwiftUI provides us just the right tool to acheive this.

**EnvironmentObject** - [https://developer.apple.com/documentation/swiftui/environmentobject](https://developer.apple.com/documentation/swiftui/environmentobject){:target="_blank"}

Next, we will update our MaterialTheme class to be an `ObservableObject` and annotate its properties with `@Published`.  This will fire change notifications in SwiftUI everytime any of the Published properties are udpated.

Make the below changes to the class definition and the `colorPalette` & `typography` properties.

```
public class MaterialTheme: ObservableObject {
    ...
    ...
    ...
    @Published public var colorPalette: ColorPalette
    @Published public var typography: Typography
}
```

Next, we will update our reusable components from previous section and apply styles to them.

In order to use the environment object, you need to add:
```
@EnvironmentObject var theme: MaterialTheme
```

into the components. We use this environment object available to us to apply Material styles to our components.

Here is a basic sample of styles applied to our components:


**HeadlineWithCaption.swift**
```
public struct HeadlineWithCaption: View {
    ...
    @EnvironmentObject var theme: MaterialTheme

    public var body: some View {
        return VStack {
            Text(title)
                .font(theme.typography.h1)
                .foregroundColor(theme.colorPalette.onBackground)
            Text(caption)
                .font(theme.typography.subtitle1)
                .foregroundColor(theme.colorPalette.onBackground)
        }
    }
}
```


**TitleWithText.swift**
```
public struct TitleWithText: View {
    ...
    @EnvironmentObject var env: AppTheme
    
    public var body: some View {
        VStack {
            Text(title)
                .font(theme.typography.h5)
                .foregroundColor(theme.colorPalette.onBackground)
            Text(text)
                .font(theme.typography.body1)
                .foregroundColor(theme.colorPalette.onBackground)
                .multilineTextAlignment(.center)
        }
    }
}
```


Lastly in our main playground file ThemeExample, we create an instance of theme and make it available as an environment object.

```
var theme = MaterialTheme(
    colorPalette: MaterialTheme.ColorPalette(),
    typography: MaterialTheme.Typography())

let view  = ThemeExampleDemo().environmentObject(theme)
PlaygroundPage.current.liveView = UIHostingController(rootView: view)
```

### Switching between themes

Switching between themes is as easy as it can be. All we have to do is instantiate the new theme and update the environment object.

```
let redColorPalette = MaterialTheme.ColorPalette(
    primary: .red,
    secondary: .red,
    primaryVariant: .red,
    secondaryVariant: .red,
    background: .red,
    onSecondary: .black,
    onBackground: .red,
    onSurface: .red)

let updatedTypography = MaterialTheme.Typography(
    h1: Font.system(size: 40).italic(),
    h2: Font.system(size: 36).italic(),
    h3: Font.system(size: 32).italic(),
    h4: Font.system(size: 24).italic(),
    h5: Font.system(size: 20).italic(),
    h6: Font.system(size: 18).italic())

theme.colorPalette = redColorPalette
theme.typography = updatedTypography

```

## Finally...

<center> 
<img src="/img/themeDemo.gif" alt="App layout with two different variations of theme applied" />
</center>


In the next dialogue, we will take this a step further and instead of instantiating the reusable components directly in our view and violating the SOLID principles of programming, we will make our container view generic and inject them instead. That will help us achieve true composability and also increase the testability of our views.
