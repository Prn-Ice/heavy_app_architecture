# Principles

- Views should never MAKE USE of a service directly.
- Views should contain zero to (preferred) no logic. If the logic is from UI only items then we do the least amount of required logic and pass the rest to the ViewModel.
- Use a consistent naming scheme through out the project. Eg camel case file names with pascal case class names.
- Each file can have at most one public widget, all other widgets must be declared as private or in another file.