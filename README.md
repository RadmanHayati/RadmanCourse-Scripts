# add edit submit button

- 🎯 what do we wanna do?

    as the title suggests we are implementing the code for the submit floating button of add and edit fragment

- 🛤️ how do we store views' input values in our saved instance state?

    we want to store the new saved instance state whenever we type sth new in the input or we check the importance checkbox
    ⦁	and we do this by assigning a new value to the correspounding variable in the viewModel by doing this the setter method of the variable will be called and as we overrided it the value would be setted in the save instance state and now everyhing makes sense

- 🧫 how to implement save (add/update) button + validation?

    ⦁	now we should save values by pressing the save word floating button in order to do this we call a method from the view model
    ⦁	in this method, we should update the items of the list and also do the validation for the input because it can not be null and if it's the case we should show a Snackbar.
    ⦁	this validation logic doesn't belong to the fragment it should be in the ViewModel.
    ⦁	ctrl + shift + enter completes the statement adds parentheses and curly brackets.
    ⦁	when we made sure that the input value is not null we should make an event in order to show a snackbar which is the only way to do this as snackbars are only available in the fragments
    ⦁	alright, now that we made sure that the input is not null we should find out if we need to call the update function or the add function.
    ⦁	and we do so by checking either if the word variable which would contain the word sent by nav args in the update situation is null or not.
    ⦁	if it was null we simply make a new object of word and pass everything to it and call another function to do tha dao operations
    ⦁	and if it wasn't null (update situation) we make a copy of the word and pass everything to it and call update function.
    ⦁	both functions should be in coroutine block (viewmodel scope) because we are doing dao operations in them
    ⦁	both functions should navigate back to the words list fragment
    ⦁	showing " word name is blank " snack bar and navigation in both functions have to be done by events so we are going to implement it now

- 🗞️ how to make an event?

    ⦁	we need a new sealed class called addEditWordEvent
    ⦁	it has two subclasses to show the invalid input message and navigate back with the result // result is a flag probably to determine which fragment we were in.
    ⦁	now we should make a channel for these events.
    ⦁	now we can use it we make a function for showing invalid input messages which takes a message and returns a coroutine block (to send an event through the channel). which looks like this

    ```kotlin
    private funshowInvalidInputMessages(msg: String) =viewModelScope.launch{
    addEditWordEventChannel.send(AddEditWordEvent.ShowInvalidInputMessage(msg))
    }
    ```

- 🔙 how can we implement navigate back result?

    ⦁	now we should implement the navigate back with the result, we make the result values in the main activity outside of the class but it also could be anywhere else.

    ```kotlin

    const valADD_WORD_RESULT_OK= Activity.RESULT_FIRST_USER
    const valEDIT_WORD_RESULT_OK= Activity.RESULT_FIRST_USER + 1
    ```

    ⦁	and we pass them to the event data class and send the event in the channel and we will take care of the rest in the fragment.
    ⦁	we need a when in the fragment to handle these different events.
    we do it in here viewLifecycleOwner.lifecycleScope.launchWhenCreated { } so we stop listening for the events when the app is in the background
    ⦁	and we use viewModel.addEditWordEvent.collect { event -> } to collect this flow because we used recieveAsFlow() function.
    ⦁	now we right when and we use .exhustive to get compile-time safety and we also use alt + enter to handle everything
    ⦁	in showInvalid InputMessege we wanted to show a snackbar
    in navigate back we use edit_text_word.clearFocus() to hide the keyboard
    ⦁	fragment result API to send result values between different fragments and we use it to send add edit result to it.
    ⦁	we write a fragment listener in the wordFragment to recieve the result.
    ⦁	we make a method in view model and pass the result to it
    ⦁	in the view model we use when if it was from add or edit we send a appropiate message to an other function which takes the message and put in a new data class in our sealed class which takes a msg:string and send as an event to our channel
    ⦁	we go back to our list fragment we add a new when option and show the message in the snackbar
