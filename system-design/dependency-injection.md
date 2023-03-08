# Dependency Injection

Dependency injection (DI) is a software design pattern that allows components (classes or functions) to declare their dependencies on other components, and then have those dependencies provided to them at runtime by an external entity, rather than having the components create or find their dependencies themselves.

The purpose of DI is to decouple the components of a system, making them easier to test, maintain, and extend. By allowing components to declare their dependencies rather than hard-coding them, it becomes easier to change the behavior of a system without having to modify the components themselves.

There are three types of dependency injection:

1. **Constructor injection:** In constructor injection, the dependencies are provided to a component through its constructor. The component declares its dependencies as constructor parameters, and an external entity creates an instance of the component and passes in the required dependencies.
2. **Setter injection**: In setter injection, the dependencies are provided to a component through setter methods. The component declares its dependencies as private member variables, and an external entity creates an instance of the component and calls the appropriate setters to provide the required dependencies.
3. **Interface injection**: In interface injection, the component declares an interface for its dependencies, and an external entity provides an implementation of that interface to the component.

### Examples in Python

Constructor injection

```python
class EmailService:
    def __init__(self, smtp_server):
        self.smtp_server = smtp_server

    def send_email(self, from_addr, to_addr, subject, body):
        # Code to send email using smtp_server

email_service = EmailService("smtp.gmail.com")
```

Setter injection

```python
class EmailService:
    def __init__(self):
        self.smtp_server = None

    def set_smtp_server(self, smtp_server):
        self.smtp_server = smtp_server

    def send_email(self, from_addr, to_addr, subject, body):
        # Code to send email using self.smtp_server

email_service = EmailService()
email_service.set_smtp_server("smtp.gmail.com")
```

Interface injection

```python
class EmailService:
    def __init__(self):
        self.email_provider = None

    def set_email_provider(self, email_provider):
        self.email_provider = email_provider

    def send_email(self, from_addr, to_addr, subject, body):
        # Code to send email using self.email_provider

class GmailProvider:
    def send_email(self, from_addr, to_addr, subject, body):
        # Code to send email using Gmail's API

email_service = EmailService()
email_provider = GmailProvider()
email_service.set_email_provider(email_provider)
```

### Examples in C++

Constructor&#x20;

```cpp
class EmailService {
public:
    EmailService(const std::string& smtp_server) : smtp_server_(smtp_server) {}

    void send_email(const std::string& from_addr, const std::string& to_addr, const std::string& subject, const std::string& body) {
        // Code to send email using smtp_server_
    }

private:
    std::string smtp_server_;
};

EmailService email_service("smtp.gmail.com");
```

Interface injection

```cpp
class EmailProvider {
public:
    virtual ~EmailProvider() {}
    virtual void send_email(const std::string& from_addr, const std::string& to_addr, const std::string& subject, const std::string& body) = 0;
};

class GmailProvider : public EmailProvider {
public:
    void send_email(const std::string& from_addr, const std::string& to_addr, const std::string& subject, const std::string& body) override {
        // Code to send email using Gmail's API
    }
};

class EmailService {
public:
    void set_email_provider(std::unique_ptr<EmailProvider> email_provider) {
        email_provider_ = std::move(email_provider);
    }

    void send_email(const std::string& from_addr, const std::string& to_addr, const std::string& subject, const std::string& body) {
        email_provider_->send_email(from_addr, to_addr, subject, body);
    }

private:
    std::unique_ptr<EmailProvider> email_provider_;
};

std::unique_ptr<EmailProvider> email_provider = std::make_unique<GmailProvider>();
EmailService email_service;
email_service.set_email_provider(std::move(email_provider));
```
