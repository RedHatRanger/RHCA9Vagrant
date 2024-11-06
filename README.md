# RHCA9Vagrant
This repository will help the candidate prepare for 5 Red Hat Exams towards the RHCA Certification for Red Hat
![{CBE5D4A2-B6F4-4F38-BDB7-771954C868DC}](https://github.com/user-attachments/assets/f995c405-4132-4bd8-8f13-4a888fcdb0c8)

<a href="https://github.com/RedHatRanger/RHCA9Vagrant/tree/main/EX342_Linux_Troubleshooting">link</a>

<!DOCTYPE html>
<html>
<head>
  <title>Interactive Quiz</title>
</head>
<body>
  <h3>Resolving Boot Loader Issues on UEFI Systems - Quiz 1</h3>

  <!-- Question 1 -->
  <div>
    <p>1. On which directory does the Anaconda installer mount the UEFI partition?</p>
    <input type="radio" name="q1" id="q1a" onclick="checkAnswer('q1', 'b')"> a. /boot/grub2<br>
    <input type="radio" name="q1" id="q1b" onclick="checkAnswer('q1', 'b')"> b. /boot/efi<br>
    <input type="radio" name="q1" id="q1c" onclick="checkAnswer('q1', 'b')"> c. /etc/default<br>
    <input type="radio" name="q1" id="q1d" onclick="checkAnswer('q1', 'b')"> d. /etc/grub.d/<br>
  </div>

  <!-- Question 2 -->
  <div>
    <p>2. Which directory stores the GRUB2 configuration files and the UEFI firmware applications?</p>
    <input type="radio" name="q2" id="q2a" onclick="checkAnswer('q2', 'c')"> a. /boot/grub2<br>
    <input type="radio" name="q2" id="q2b" onclick="checkAnswer('q2', 'c')"> b. /etc/default<br>
    <input type="radio" name="q2" id="q2c" onclick="checkAnswer('q2', 'c')"> c. /boot/efi/EFI/redhat<br>
    <input type="radio" name="q2" id="q2d" onclick="checkAnswer('q2', 'c')"> d. /boot/loader<br>
  </div>

  <!-- Question 3 -->
  <div>
    <p>3. Which packages are needed to reinstall deleted files from /boot/efi on UEFI-based systems?</p>
    <input type="radio" name="q3" id="q3a" onclick="checkAnswer('q3', 'a')"> a. grub2-efi and shim<br>
    <input type="radio" name="q3" id="q3b" onclick="checkAnswer('q3', 'a')"> b. grub2-mkconfig and grub2-efi<br>
    <input type="radio" name="q3" id="q3c" onclick="checkAnswer('q3', 'a')"> c. shim and grub2-install<br>
    <input type="radio" name="q3" id="q3d" onclick="checkAnswer('q3', 'a')"> d. grub2-efi and grub2-install<br>
  </div>

  <!-- Question 4 -->
  <div>
    <p>4. Which command sets the default kernel?</p>
    <input type="radio" name="q4" id="q4a" onclick="checkAnswer('q4', 'd')"> a. grub2-mkconfig --default<br>
    <input type="radio" name="q4" id="q4b" onclick="checkAnswer('q4', 'd')"> b. kernel-install add<br>
    <input type="radio" name="q4" id="q4c" onclick="checkAnswer('q4', 'd')"> c. grub2-install<br>
    <input type="radio" name="q4" id="q4d" onclick="checkAnswer('q4', 'd')"> d. grubby --set-default<br>
  </div>

  <script>
    function checkAnswer(questionId, correctAnswer) {
      let selectedOption = document.querySelector(`input[name="${questionId}"]:checked`).id;
      let selectedValue = selectedOption.slice(selectedOption.length - 1); // Extracting the option letter (a, b, c, d)
      if (selectedValue === correctAnswer) {
        alert("Correct answer!");
      } else {
        alert("Incorrect answer, try again.");
      }
    }
  </script>
</body>
</html>
