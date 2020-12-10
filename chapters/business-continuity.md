# Concepts

- Business continuity
- Disaster recovery
- Fault tolerance
- High availability
- SLA
    - it's an agreed goal, not a guarantee;
    - service credits can be claimed when SLA is breached
- Recovery time objective (RTO)
- Recovery point objective (RPO)
- Types of failure
    - Hardware failure
    - Deployment failure
    - Load induced
    - Data induced
    - Credential expiration (SSL/TLS certificate expires)
    - Dependency
    - Infrastructure
    - Identifier exhaustion
    - Human error
- Disaster recovery architectures
    - Backup and restore
    - Pilot light
    - Warm standby
    - Multi site
- RAID levels
    - RAID0 = no redundancy; RAID1 = one copy.
    - EBS doesn't work nicely with RAID5 and RAID6. Uses RAID0 or RAID1 instead
- S3 storage class
    - Standard storage class: 99.99%
    - Standard infrequent access: 99.9%
    - One-zone infrequent access: 99.5%
    - *all types have 11-9 durability*
- EFS
    - multi-AZ redundancy
- Multi-AZ RDS + multi-region read replicas
- Redshift doesn't have multi-AZ support
- Memcached doesn't support multi-AZ, but Redis does
- Direct connect is not HA by default
- Failure modes and effect analysis (FMEA)
    - Severity * probability * rate of detection = risk priority number
- Spread placement group vs. cluster placement group




# Notes

1. EBS replicated automatically within a single AZ; vulnerable to AZ failure. 
2. EBS snapshots stored in S3 is multi-AZ durable. The snapshots can be copied to other regions as well.
3. AWS storage gateway for continuous data transfer from on-premise system; AWS Snowball for batch transfer.
4. Guarantees of compute HA is through reserved instances.    
5. EBS is about 10x more expensive than S3, so be careful of the choices that use it.
6. [DR strategies](https://www.cbtnuggets.com/blog/certifications/cloud/aws-disaster-recovery-pilot-light-warm-standby-multi-site)
