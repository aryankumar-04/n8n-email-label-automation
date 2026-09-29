# Contributing to Email Lable Automation

Thank you for your interest in improving **Email Lable Automation**! We welcome community contributions, suggestions, and enhancements.

## How to Contribute

1. **Fork the Repository**: Create your own copy of the repo on GitHub.
2. **Create a Feature Branch**:
   ```bash
   git checkout -b feature/your-enhancement
   ```
3. **Make and Test Your Changes**:
   - Test workflow adjustments in your local n8n environment.
   - Ensure node logic, connections, and error handling remain solid.
   - **Crucial Rule**: Completely sanitize your exported JSON! Remove all credential IDs, webhook IDs, instance IDs, and personal static data before committing.
4. **Commit & Push**:
   ```bash
   git commit -m "feat: describe your change cleanly"
   git push origin feature/your-enhancement
   ```
5. **Open a Pull Request**: Submit your PR with a concise description of the motivation, changes made, and test results.

## Reporting Issues & Feedback
If you encounter a bug or have a feature idea, please open an Issue with:
- A clear description of the problem or proposed feature.
- Steps to reproduce (for bugs).
- Your n8n version and hosting environment.

Thank you for helping make open-source workflow automation better for everyone!
