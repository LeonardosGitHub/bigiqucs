# bigiqucs


Pre-Reqs:
    - Add device(s) to BIG-IQ
    - Create scheduled back-up
    - Secure vault value? https://support.f5.com/csp/article/K9420

During re-pave event
    - Generate ucs backups using scheduled backup created previously
    - Transfer UCS from BIG-IQ to ansible host
    - pave device
    - upload ucs to re-pave'd "factory reset" device - using ansible module "bigip_ucs"
    - Perform UCS restore - using ansible module "bigip_ucs"
    - re-discover device in big-iq - using ansible module "bigiq_device_discovery"
