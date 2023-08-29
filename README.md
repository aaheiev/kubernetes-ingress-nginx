# kubernetes-ingress-nginx

Terraform module to install ingress-nginx to the Kubernetes cluster

## Example usage

```terraform
resource "azurerm_public_ip" "ingress_nginx_public_ip" {
  name                = "${var.aks_name}-ingress-nginx"
  resource_group_name = module.aks.node_resource_group
  location            = var.location
  allocation_method   = "Static"
  sku                 = "Standard"
  zones               = ["1", "2", "3"]
  ip_tags             = {}
  tags = {
    k8s-azure-cluster-name = var.aks_name
    k8s-azure-service      = "ingress-nginx/ingress-nginx-controller"
  }
}

module "ingress-nginx" {
  depends_on        = [module.aks, azurerm_public_ip.ingress_nginx_public_ip]
  source            = "github.com/aaheiev/kubernetes-ingress-nginx?ref=4.7.1"
  public_ip_address = azurerm_public_ip.ingress_nginx_public_ip.ip_address
  providers = {
    helm       = helm
    kubernetes = kubernetes
  }
}
```