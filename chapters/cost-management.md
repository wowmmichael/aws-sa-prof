# Concepts

- CapEx vs. OpEx
- Total cost of ownership (TCO)
- Return of investment (ROI)
- Optimization strategies
    - Appropriate Provisioning
    - Right-sizing
    - Purchase options
    - Geographic selection
    - Managed service
    - Optimized data transfer
- EC2 Fleet (Mixed reserved, on-demand, and spot instances)
- Tagging resource
- Reserved instance types: Standard, Convertible, Scheduled
    - Standard vs. Convertible: Change instance family, Tenancy, OS, Payment option, Benefit from Price reduction
- Spot instance types: fire-and-kill, maintained, duration-based
    - fire-and-kill: instance is killed and ephemeral data is lost
    - maintained: instance can be configured to either terminate, stop, or hibernate
- AWS Budgets
- Trusted advisor
- Consolidated Billing is a feature of AWS Organizations


# Notes

1. Tags can be enforced by AWS Config Rules or custom scripts.
2. For RI, if AZ selected, instances are reserved for the zone; Otherwise, availability is not guaranteed.
3. Make smart choices about AZ when using spot instances because the demand and price may vary.
4. Consolidated billing to take advantage of tiered billing.
5. CloudTrail is a tool to monitor API access
6. ELB is capable for doing cross-zone load-balancing.
7. RI cannot be moved across regions.
8. Dedicated host reserve capacity; dedicated instances can be run on RI and spot instances. The difference is mostly on trackability of core, socket, vms. Dedicated host is needed to comply with vendor's software license. [ref](https://aws.amazon.com/ec2/dedicated-hosts/#Comparing_Dedicated_Hosts_to_Dedicated_Instances)
9. Regional RI, Zonal RI; [ref about definition](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-scope.html) and [ref about how they are applied](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/apply_ri.html).
