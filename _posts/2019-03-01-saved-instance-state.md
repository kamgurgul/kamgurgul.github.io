---
title: Understanding Android SavedState and SavedStateHandle
excerpt: "Managing app state effectively is key to building robust Android applications, especially during lifecycle events like configuration changes or process death. In this post, we explore SavedState and SavedStateHandle, two important tools that simplify state management. We'll cover how SavedStateHandle integrates with ViewModel, making it easier to preserve UI-related data seamlessly. With practical examples involving StateFlow, you'll learn how to maintain user input and create a smoother user experience without the hassle of managing Bundles manually."
tags: [android, internals]
---

# Understanding Android SavedState and SavedStateHandle

Building robust Android applications often requires careful handling of app states, especially when the lifecycle of activities or fragments comes into play. The Android platform offers several tools to manage state across configuration changes or process death, with two of the most common being `SavedState` and `SavedStateHandle`. In this blog post, we’ll take a closer look at these two concepts, explaining how they work and when you should use them.

## What is SavedState?

In Android, `SavedState` refers to the mechanism that allows you to preserve critical information when an activity or fragment is recreated. This typically happens during configuration changes such as screen rotation, or when the app is killed by the system and needs to be restored later.

Traditionally, Android developers relied on the `onSaveInstanceState` method to store information in a `Bundle`, and then used that `Bundle` to restore the state in `onCreate` or `onViewStateRestored`. However, this approach can get cumbersome, especially when dealing with complex data or larger-scale applications.

## Enter SavedStateHandle

The `SavedStateHandle` is a more modern and convenient approach introduced as part of Android's Jetpack components. It's particularly useful when working with `ViewModel` and `Fragment` components, providing a seamless way to save and retrieve UI-related data even after process death.

### How SavedStateHandle Works

`SavedStateHandle` is essentially a key-value map that allows you to store small pieces of data that should survive configuration changes or process death. It works closely with the `ViewModel` to ensure the state persists beyond the usual lifecycle of a fragment or activity. Instead of manually handling `Bundle` objects, you can interact with `SavedStateHandle` in a more straightforward way.

Under the hood, `SavedStateHandle` relies on the `SavedStateRegistry` mechanism provided by the Android framework. When a `ViewModel` is created, it is given a reference to a `SavedStateHandle` that is linked to the `SavedStateRegistryOwner` (typically an activity or fragment). The `SavedStateRegistry` manages the state by storing key-value pairs in a `Bundle` when the lifecycle owner is stopped, and then restores those values when it is recreated. This ensures that the data is retained across configuration changes and even process death, making it a powerful tool for state management.

For example, consider the following usage in a `ViewModel`:

```kotlin
class MyViewModel(
    private val savedStateHandle: SavedStateHandle,
) : ViewModel() {

    fun setUserName(name: String) {
        savedStateHandle[KEY_USER_NAME] = name
    }

    companion object {
        private const val KEY_USER_NAME = "user_name"
    }
}
```

Here, `SavedStateHandle` allows you to save the user name with a specific key, and retrieve it later, simplifying the process of managing UI state in a configuration-safe manner.

### Benefits of Using SavedStateHandle

1. **Integration with ViewModel:** Unlike traditional `onSaveInstanceState`, `SavedStateHandle` is integrated with `ViewModel`. This means it benefits from the `ViewModel`'s lifecycle awareness, making it easier to maintain state during configuration changes.

2. **Simplified Code:** No more manual handling of `Bundles` and lifecycle methods. The API is intuitive, allowing you to focus on the logic rather than lifecycle intricacies.

3. **Automatic Process Death Handling:** `SavedStateHandle` automatically manages saving and restoring data in cases of process death, which can be complex to handle manually.

### When to Use SavedStateHandle

- **Fragments with ViewModel:** When you have a fragment that uses a `ViewModel`, `SavedStateHandle` is ideal for preserving UI-related state between fragment recreations.
- **Configuration Changes:** If you need to persist small amounts of data, like user input or filter criteria, during configuration changes, `SavedStateHandle` can simplify your implementation.
- **State Restoration After Process Death:** If you want your application to restore its state seamlessly after the OS terminates your app to free up resources, `SavedStateHandle` can be your go-to tool.

## Practical Example: Saving User Input

Let's consider a fragment where a user types their name into a text field. You can easily manage this state with `SavedStateHandle` and `StateFlow` to ensure the data persists across configuration changes.

```kotlin
class MyViewModel(
    private val savedStateHandle: SavedStateHandle,
) : ViewModel() {

    private val _uiStateFlow = MutableStateFlow(
        UiState(
            userInput = savedStateHandle["user_input"] = input
        )
    )
    val uiStateFlow = _uiStateFlow.asStateFlow()

    fun saveUserInput(input: String) {
        savedStateHandle["user_input"] = input
        _uiStateFlow.update {
            it.copy(userInput = input)
        }
    }

    data class UiState(
        val userInput: String? = null,
    )
}
```

In your fragment, you would observe this data and update the UI accordingly:

```kotlin
class MyFragment : Fragment() {

    private val viewModel: MyViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val userInputEditText = view.findViewById<EditText>(R.id.userInputEditText)

        // Observe the saved input
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiStateFlow.collect { uiState ->
                    userInputEditText.setText(uiState.userInput ?: "")
                }
            }
        }
    }
}
```

With this approach, user input is automatically saved and restored, creating a smooth user experience without manually managing bundles and lifecycle intricacies.

## Conclusion

`SavedState` and `SavedStateHandle` are essential tools for Android developers aiming to create more resilient and lifecycle-aware applications. While `SavedState` using `onSaveInstanceState` is still a valid approach, `SavedStateHandle` provides a cleaner and more integrated solution, especially when working with Jetpack's `ViewModel` and `Fragment` components. By leveraging these tools, you can ensure that your app handles configuration changes and process death smoothly, leading to a better user experience.

