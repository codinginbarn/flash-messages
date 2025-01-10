<div align="center">

# Flash Messages Package

![Tests](https://github.com/nassiry/flash-messages/actions/workflows/tests.yml/badge.svg)
![Packagist Downloads](https://img.shields.io/packagist/dt/nassiry/flash-messages)
![Packagist Version](https://img.shields.io/packagist/v/nassiry/flash-messages)
![PHP](https://img.shields.io/badge/PHP-%5E7.4-blue)
![License](https://img.shields.io/github/license/nassiry/flash-messages)

</div>

A lightweight PHP library for handling flash messages with **session storage** & simple **HTML** rendering. This package does not depend on any framework, but it can be integrated with frameworks like **Laravel**, **Symfony**, **CakePHP**, and **CodeIgniter** if needed.

### Features
- Session-based flash message storage.
- Instant message rendering.
- Custom message types.
- Fully framework-agnostic.

## Installation

The recommended way use Composer to install the package:

```
composer require nassiry/flash-messages
```

## Usage

By default, the package stores messages in sessions to be displayed on the next page load.
### 1. Default Usage (Display on Next Page Load)

```php
require __DIR__ . '/vendor/autoload.php';

use Nassiry\FlashMessages\FlashMessages;

// Create an instance
$flash = FlashMessages::create();

// Add flash messages to be displayed on the next page load
$flash->success('Operation successful!');
$flash->error('An error occurred.');

// Render messages on the next page template file
$flash->render();
```
> **Note**: Ensure that `session_start();` has been called in your script before using.
### 2. Current Page Usage (Display Immediately)

To display a message on the current page, set the second argument to `true` (default is `false`):

```php
require __DIR__ . '/vendor/autoload.php';

use Nassiry\FlashMessages\FlashMessages;

// Create an instance
$flash = FlashMessages::create();

// Add flash messages to be displayed immediately
$flash->error('An error occurred.', true);

// Render messages instantly in the same page
$flash->render();
```

### 3. Checking for Messages

You can check if there are any stored messages:

```php
if ($flash->hasMessages()) {
    echo "There are flash messages available.";
}
```

### 4. Retrieving Messages

You can retrieve all stored messages as an array:

```php
$messages = $flash->getMessages();
foreach ($messages as $message) {
    echo $message['type'] . ': ' . $message['message'] . "<br>";
}
```

### 5. Rendering Messages
Outputs all flash messages with default HTML structure

```php
$flash->render();
```
```html
<div class="flash flash-success">Operation successful!</div>
<div class="flash flash-error">An error occurred!</div>
```

### 6. Clearing Messages

To clear all stored messages:

```php
$flash->clear();
```

### Adding Different Types of Messages
Currently, the package support following message types.
 - `$flash->success('Success message!');`
 - `$flash->error('Error message!');`
 - `$flash->info('Informational message!');`
 - `$flash->warning('Warning message!');`
#### Adding a custom type message
 - `addCustomType(string $type, string $message, bool $instant = false)`
```php
$flash->addCustomType('notification',
 'This is a custom notification message!',
  true
 );
$flash->addCustomType('alert',
 'This is an alert message!',
  false
);
```

### Custom Rendering Logic

If you need to customize the way messages are displayed, extend the `FlashMessageRenderer` class:

```php
use Nassiry\FlashMessages\FlashMessageRenderer;

class CustomRenderer extends FlashMessageRenderer
{
    public function renderMessage(string $type, string $message): void
    {
        echo sprintf('<div class="custom-%s">%s</div>', htmlspecialchars($type), htmlspecialchars($message));
    }
}

$renderer = new CustomRenderer();
$flash = new FlashMessages(new FlashMessageStorage(), $renderer);
$flash->success('Custom rendered message!');
$flash->render();
```

### Testing

Run the unit tests to ensure the package works as expected:
#### 1. Prerequisites
Ensure you have all dependencies installed by running:
```
composer install
```
#### 2. Running Tests
Execute the following command to run the test suite:

```
composer test
```

Here are somepractical examples of how to use it. Let me break this down into clear sections:

#### Overview
This is a lightweight PHP library for handling flash messages (temporary messages shown to users). It's framework-agnostic but can work with popular PHP frameworks like Laravel, Symfony, CakePHP, and CodeIgniter.

#### Key Features
- Session-based storage
- Immediate or delayed message display
- Custom message types
- Framework independence
- Simple HTML rendering

Here are some practical examples of how to use this package:

#### 1. Basic Setup
First, install the package using Composer:
```php
composer require nassiry/flash-messages
```

#### 2. Simple Implementation
Here's a basic example showing how to use flash messages:

```php
<?php
// First, make sure to start the session
session_start();

// Include autoloader
require __DIR__ . '/vendor/autoload.php';

use Nassiry\FlashMessages\FlashMessages;

// Create flash message instance
$flash = FlashMessages::create();

// Add some messages
$flash->success('Your profile has been updated!');
$flash->error('Please fill in all required fields.');
$flash->info('Don't forget to verify your email.');

// Render the messages
$flash->render();
```

#### 3. Practical Use Case - Form Submission
Here's a real-world example of handling form submission:

```php
<?php
session_start();
require __DIR__ . '/vendor/autoload.php';

use Nassiry\FlashMessages\FlashMessages;

$flash = FlashMessages::create();

// Handle form submission
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    try {
        // Your form processing logic here
        if (empty($_POST['email'])) {
            $flash->error('Email is required', true); // Show immediately
        } else {
            // Process successful submission
            $flash->success('Form submitted successfully!');
            header('Location: thank-you.php');
            exit;
        }
    } catch (Exception $e) {
        $flash->error('An error occurred: ' . $e->getMessage());
    }
}
?>

<!-- HTML Form -->
<form method="POST">
    <?php $flash->render(); // Render any flash messages ?>
    <input type="email" name="email" placeholder="Enter your email">
    <button type="submit">Submit</button>
</form>
```

#### 4. Custom Renderer Example
Here's how to create a custom renderer with Bootstrap styling:

```php
<?php
use Nassiry\FlashMessages\FlashMessageRenderer;

class BootstrapRenderer extends FlashMessageRenderer
{
    public function renderMessage(string $type, string $message): void
    {
        $bootstrapClass = match($type) {
            'success' => 'alert-success',
            'error' => 'alert-danger',
            'info' => 'alert-info',
            'warning' => 'alert-warning',
            default => 'alert-secondary'
        };
        
        echo sprintf(
            '<div class="alert %s alert-dismissible fade show" role="alert">
                %s
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>',
            htmlspecialchars($bootstrapClass),
            htmlspecialchars($message)
        );
    }
}

// Usage
$renderer = new BootstrapRenderer();
$flash = new FlashMessages(new FlashMessageStorage(), $renderer);
```

#### 5. Ajax Implementation
Here's how to handle flash messages with Ajax:

```php
<?php
// ajax-handler.php
session_start();
require __DIR__ . '/vendor/autoload.php';

use Nassiry\FlashMessages\FlashMessages;

$flash = FlashMessages::create();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Process Ajax request
    try {
        // Your processing logic here
        $flash->success('Operation completed successfully', true);
        echo json_encode(['status' => 'success', 'messages' => $flash->getMessages()]);
    } catch (Exception $e) {
        $flash->error($e->getMessage(), true);
        echo json_encode(['status' => 'error', 'messages' => $flash->getMessages()]);
    }
}
```

```javascript
// JavaScript to handle Ajax response
fetch('ajax-handler.php', {
    method: 'POST',
    body: formData
})
.then(response => response.json())
.then(data => {
    if (data.messages) {
        data.messages.forEach(msg => {
            // Display message using your preferred UI method
            showFlashMessage(msg.type, msg.message);
        });
    }
});
```

These examples should give you a good starting point for implementing flash messages in your PHP applications using this package.

### License

This package is open-source software licensed under the [MIT license](LICENSE).

### Contributing

Contributions are welcome! Please feel free to submit a Pull Request or open an issue.
