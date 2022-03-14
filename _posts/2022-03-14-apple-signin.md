---
layout: single
title: "Day 1 : Apple Sign In Authentication"
classes: wide
---

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/apple-signin.jpg){: .align-center}

Before writing the code **add sign in capability**.

```swift
import AuthenticationServices
```

```swift
class ViewController: UIViewController {

    // Sign in button with AppleIDButton
    private let signInButton = ASAuthorizationAppleIDButton()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemPink

    // Adding the buton into subview & behavior
        view.addSubview(signInButton)
        signInButton.addTarget(self, action: #selector(didTabSignIn), for: .touchUpInside)  
    }
    // Override subview function
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

    // Create the button with CGRect
        signInButton.frame = CGRect(x: 0, y: 0, width: 250, height: 50)
        signInButton.center = view.center
    }

    // Object-C from selector = use @objc
    @objc func didTabSignIn() {
        let provider = ASAuthorizationAppleIDProvider()
        let request = provider.createRequest()
        request.requestedScopes = [.fullName, .email]
        
        let controller = ASAuthorizationController(authorizationRequests: [request])
        controller.delegate = self
        controller.presentationContextProvider = self
        controller.performRequests()
    }
}
```

```swift
// Extension for error handling
extension ViewController: ASAuthorizationControllerDelegate {
    func authorizationController(controller: ASAuthorizationController, didCompleteWithError error: Error) {
        print("Failed!")
    }
    func authorizationController(controller: ASAuthorizationController, didCompleteWithAuthorization authorization: ASAuthorization) {
        switch authorization.credential {
        case let credentials as ASAuthorizationAppleIDCredential:
            _ = credentials.fullName?.givenName
            _ = credentials.fullName?.familyName
            _ = credentials.email
            break
        default:
            break
        }
    }
}

extension ViewController: ASAuthorizationControllerPresentationContextProviding {
    func presentationAnchor(for controller: ASAuthorizationController) -> ASPresentationAnchor {
        return view.window!
    }
}
```