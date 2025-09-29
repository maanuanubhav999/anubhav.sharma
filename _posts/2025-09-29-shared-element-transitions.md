---
title: "Shared Element Transitions in Android: A Simple Example"
date: 2025-09-29 10:00:00 +0530
categories: [android, ui]
tags: [shared element, animation, android, transitions]
author: <1>
---

Shared element transitions are a powerful way to create smooth, visually appealing navigation between screens in Android apps. They allow you to animate a UI element (like an image or a card) seamlessly from one Component to another, making your app feel more dynamic and modern.

## What is a Shared Element Transition?

A shared element transition animates a view (such as an image or text) from one state to another, maintaining continuity and context for the user. This is commonly used in gallery apps, profile screens, or anywhere you want to highlight a particular element during navigation.

## Jetpack Compose Example

Jetpack Compose makes shared element transitions easy with the `SharedTransitionLayout`, `Modifier.sharedElement()`, and `Modifier.sharedBounds()` APIs.

**Key Components:**
- `SharedTransitionLayout`: The outermost layout required to implement shared element transitions. Items that want to share the animation need to be inside this scope.
- `Modifier.sharedElement()`: Marks the element to be animated between screens.
- `Modifier.sharedBounds()`: Defines the bounds of the component to be shared. This can be visually different in both states.

**Simple Example:**

```kotlin
@OptIn(ExperimentalSharedTransitionApi::class)
@Composable
fun SharedElementTransitionExample() {
    var showDetails by remember { mutableStateOf(false) }

    SharedTransitionLayout {
        AnimatedContent(
            showDetails,
            label = "basic_transition"
        ) { targetState ->
            if (!targetState) {
                MainContent(
                    onShowDetails = {
                        showDetails = true
                    },
                    animatedVisibilityScope = this@AnimatedContent,
                    sharedTransitionScope = this@SharedTransitionLayout
                )
            } else {
                DetailsContent(
                    onBack = {
                        showDetails = false
                    },
                    animatedVisibilityScope = this@AnimatedContent,
                    sharedTransitionScope = this@SharedTransitionLayout
                )
            }
        }
    }
}

@OptIn(ExperimentalSharedTransitionApi::class)
@Composable
fun MainContent(
    onShowDetails: () -> Unit,
    animatedVisibilityScope: AnimatedVisibilityScope,
    sharedTransitionScope: SharedTransitionScope
) {
    with(sharedTransitionScope) {
        Box(
            modifier = Modifier
                .sharedBounds(
                    rememberSharedContentState(key = "bounds"),
                    animatedVisibilityScope = animatedVisibilityScope
                )
                .background(Color(0xFF6200EE))
                .size(150.dp)
                .clickable { onShowDetails() },
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "Tap Me!",
                modifier = Modifier
                    .sharedElement(
                        rememberSharedContentState(key = "text"),
                        animatedVisibilityScope = animatedVisibilityScope
                    ),
                color = Color.White,
                fontSize = 18.sp
            )
        }
    }
}

@OptIn(ExperimentalSharedTransitionApi::class)
@Composable
fun DetailsContent(
    onBack: () -> Unit,
    animatedVisibilityScope: AnimatedVisibilityScope,
    sharedTransitionScope: SharedTransitionScope
) {
    with(sharedTransitionScope) {
        Box(
            modifier = Modifier
                .sharedBounds(
                    rememberSharedContentState(key = "bounds"),
                    animatedVisibilityScope = animatedVisibilityScope
                )
                .fillMaxWidth()
                .height(300.dp)
                .clip(CircleShape)

                .background(Color(0xFF03DAC5))

                .clickable { onBack() },
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "Details View\nTap to go back",
                modifier = Modifier
                    .sharedElement(
                        rememberSharedContentState(key = "text"),
                        animatedVisibilityScope = animatedVisibilityScope
                    ),
                color = Color.Black,
                fontSize = 24.sp
            )
        }
    }
}
```

This code shows a simple row with a shared image. When you navigate between screens, the image animates smoothly using shared element transitions.

## Video Preview

Here's a quick demo of shared element transitions in action:

[![Shared Element Transition Demo](https://img.youtube.com/vi/5L0k7gm9Cgc/0.jpg)](https://youtube.com/shorts/5L0k7gm9Cgc)

> Click the image above to watch the video on YouTube.

---

**Links:**
- [Official Android Docs](https://developer.android.com/develop/ui/compose/animation/shared-elements)


---