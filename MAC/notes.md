#!/bin/bash
sudo dseditgroup -o edit -a $username_to_add -t user admin
sudo dseditgroup -o edit -a $username_to_add -t user wheel
