# Test Diagram

```mermaid
graph LR
    Start -->|path 1| Process_A[Process A]
    Start -->|path 2| Process_B[Process B]
    Process_A --> Merge
    Process_B --> Merge
    Merge -->|done| End
```
