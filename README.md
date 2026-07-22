# OpenABE

OpenABE is a professional-grade C++ cryptographic library that implements various Attribute-Based Encryption (ABE) schemes and other industry-standard cryptographic primitives. It allows developers to implement flexible, policy-based access control without requiring deep expertise in cryptography.

## Features
* **Flexible Access Control**: Supports Key-Policy ABE (KP-ABE), Ciphertext-Policy ABE (CP-ABE), and Multi-Authority ABE.
* **High Security**: Implements Collusion-Resistance and Chosen Ciphertext Attack (CCA) security.
* **Comprehensive Primitives**: Includes Public Key Encryption (PKE), Symmetric Key Encryption (SKE), Digital Signatures, and X.509 certificate handling.
* **Optimized Performance**: Uses Barreto-Naehrig (BN) curves for efficient pairing operations.

## Repository Structure
* `src/`: Core library source code.
* `include/`: Public header files.
* `cli/`: Command-line interface tools for testing and deployment.
* `examples/`: Example applications demonstrating ABE usage.
* `bindings/`: Language bindings (e.g., Python).
* `deps/`: External dependencies (RELIC, OpenSSL, gtest).
* `docs/`: Project documentation.

## Quick Start

### Installation
1. Install system dependencies:
   ```bash
   sudo ./deps/install_pkgs.sh
   ```
2. Build the project:
   ```bash
   . ./env
   make
   make test
   ```
3. (Optional) Install to system:
   ```bash
   sudo make install
   ```

### Running Examples
Build and run the high-level API examples:
```bash
make examples
cd examples/
./test_cp  # Ciphertext-Policy ABE example
./test_kp  # Key-Policy ABE example
./test_pk  # Public Key Encryption example
```

## Documentation
Detailed documentation is available in the `docs/` directory:
* [API Specification](docs/en/api_spec.md)
* [Design Document](docs/en/design_doc.md)
* [Usage Guide](docs/en/usage_guide.md)
* [Error Documentation](docs/en/error_docs.md)
* [Class Diagrams](docs/en/diagrams.md)

## License
OpenABE is licensed under the AGPL 3.0 license.
