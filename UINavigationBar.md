# Giriş

### Navigation Çubugu Renginin Ayarlanması
```
import UIKit

// MARK: - UINavigationController Extension
extension UINavigationController {
    
    /// Sets a custom appearance for the navigation bar with a colored background.
    func applyColoredNavigationBarAppearance() {
        let appearance = UINavigationBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundImage = UIImage(named: "NavBarImage")
        appearance.titleTextAttributes = [.foregroundColor: UIColor.white]
        navigationBar.tintColor = .white
        setNavigationBarAppearance(appearance)
    }
    
    /// Resets the navigation bar appearance to a default, transparent style.
    func resetNavigationBarAppearance() {
        let appearance = UINavigationBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.titleTextAttributes = [.foregroundColor: AppColor.onSurface]
        appearance.backgroundColor = AppColor.surface
        navigationBar.tintColor = AppColor.onSurface
        setNavigationBarAppearance(appearance)
    }
    
    /// Updates the navigation bar's appearance for all states.
    private func setNavigationBarAppearance(_ appearance: UINavigationBarAppearance) {
        navigationBar.standardAppearance = appearance
        navigationBar.compactAppearance = appearance
        navigationBar.scrollEdgeAppearance = appearance
    }
}

// MARK: - Protocol for Navigation Bar Appearance
protocol WhiteNavigationBarAppearance {}

// MARK: - Base View Controller
class BaseViewController: UIViewController {
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        configureNavigationBarAppearance()
    }
    
    /// Configures navigation bar appearance based on the view controller's type.
    private func configureNavigationBarAppearance() {
        if self is WhiteNavigationBarAppearance {
            navigationController?.resetNavigationBarAppearance()
        } else {
            navigationController?.applyColoredNavigationBarAppearance()
        }
    }

}

```
### Kod Yapısının Açıklaması
1. UINavigationController Uzantısı

    - applyColoredNavigationBarAppearance(): Navigasyon çubuğuna renkli bir görünüm kazandırır.
    - resetNavigationBarAppearance(): Çubuğun varsayılan, şeffaf görünümüne dönmesini sağlar.
    - setNavigationBarAppearance(_:): Ortak işlemleri bir yardımcı fonksiyon ile sadeleştirir.
    
    Bu yaklaşım, navigasyon çubuğu görünümüne ait her işlemi tek bir yerde yönetmenize olanak tanır.

1. WhiteNavigationBarAppearance Protokolü

    - Bu protokol, bir kontrolcünün beyaz temalı bir navigasyon çubuğuna ihtiyaç duyduğunu belirtir.
    - Protokol, yalnızca bir işaretçi görevi görür ve BaseViewController tarafından kontrol edilir.
1. BaseViewController

    -   viewWillAppear(_:): Protokol tabanlı dinamik bir yapı kullanılarak navigasyon çubuğu görünümü otomatik olarak ayarlanır.

## Kodun Avantajları
- Dinamik Özelleştirme: Görünüm yönetimi, yalnızca protokol uygulamasıyla kontrol edilebilir.
- Tekrardan Kaçınma: Ortak işlemler merkezi bir uzantıda toplandığı için her kontrolcüde aynı kod yazılmak zorunda kalmaz.
- "Geleceğe Uyum: UINavigationBarAppearance kullanımı, modern iOS sürümleriyle uyumluluğu artırır.

### Örnek Kullanım:

```

class HomeViewController: BaseViewController, WhiteNavigationBarAppearance {
    // Beyaz temalı navigasyon çubuğu
}

class DetailsViewController: BaseViewController {
    // Renkli temalı navigasyon çubuğu
}
```

## Navagatiob Bar Back Button yazısını kaldırma

```
import UIKit


class BaseViewController: UIViewController {
    

    override func viewDidLoad() {
        super.viewDidLoad()
        configureBackButtonAppearance()
    }
    
    /// Sets a minimal style for the back button.
    private func configureBackButtonAppearance() {
        if #available(iOS 14.0, *) {
            navigationItem.backButtonDisplayMode = .minimal
        } else {
            navigationItem.backButtonTitle = ""
        }
    }
}

```

### Kod yapısının açıklanması
1. BaseViewController
    -   configureBackButtonAppearance(): Geri butonunu minimal bir tasarımla yapılandırır.


## NavigationBar Custom Height

```
import UIKit
class CustomNavigationBar: UINavigationBar {

    var preferredHeight: CGFloat = 64

      override var frame: CGRect {
          get {
              return super.frame
          } set {
              var frame = newValue
              frame.size.height = preferredHeight
              super.frame = frame
          }
      }
      
      override func layoutSubviews() {
          super.layoutSubviews()
          frame = CGRect(x: frame.minX, y: frame.minY, width: frame.width, height: preferredHeight)
      }
}

extension UINavigationController {
    convenience init(rootViewController: UIViewController) {
        self.init(navigationBarClass: CustomNavigationBar.self, toolbarClass: nil )
        self.setViewControllers([rootViewController], animated: false)
       }
}

```

```
class BaseViewController: UIViewController {

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
          if let navigationBar = navigationController?.navigationBar as? CustomNavigationBar {
            let barHeight = navigationBar.frame.height
            self.additionalSafeAreaInsets.top = barHeight - 44 // default navbar height
        }
    }
  
    
```
### Kod yapısının açıklanması
1. CustomNavigationBar
    -   UINavigationBar dan kalıtım alınarak custom sınıf oluşturulur ve NavgationController oluşturulurklen custom sınıf kullanılır.
1. BaseViewController
    -   navigationBar eğer CustomNavigationBar ise view in başkangıç noktasını aşağıya alır

### Örnek Kullanım:

```

let navigationController = UINavigationController(navigationBarClass: NavigationBar.self, toolbarClass: nil)
(navigationController.navigationBar as! NavigationBar).preferredHeight = 88
navigationController.setViewControllers([ViewController()], animated: false)

```