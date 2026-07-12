## Stress test

```bash
for i in {1..1000}; do curl -s https://web-mahi.azurewebsites.net/ > /dev/null & done
```
