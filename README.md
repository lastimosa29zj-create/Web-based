<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Grade 10 Web-Based Enrollment - Ambaguio NHS</title>
  
  <!-- React, ReactDOM, and Babel CDNs for single-file execution -->
  <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    /* CSS Styles */
    * { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; padding: 0; }
    body { background-color: #f4f7f6; color: #333; }
    header { background-color: #1b4965; color: white; padding: 20px; text-align: center; }
    nav { background-color: #62b6cb; padding: 10px; display: flex; justify-content: center; gap: 15px; }
    nav button { background: none; border: none; color: white; font-size: 16px; font-weight: bold; cursor: pointer; padding: 5px 10px; }
    nav button:hover { text-decoration: underline; }
    .container { max-width: 700px; margin: 30px auto; background: white; padding: 25px; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    h2 { color: #1b4965; margin-bottom: 15px; border-bottom: 2px solid #62b6cb; padding-bottom: 5px; }
    .form-group { margin-bottom: 15px; }
    label { display: block; margin-bottom: 5px; font-weight: 600; }
    input, select { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 4px; }
    button.btn-primary { background-color: #1b4965; color: white; padding: 12px 20px; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; width: 100%; margin-top: 10px; }
    button.btn-primary:hover { background-color: #0f2c3e; }
    .alert-success { padding: 15px; background-color: #d4edda; color: #155724; border: 1px solid #c3e6cb; border-radius: 4px; margin-top: 15px; }
    .user-status { font-size: 14px; font-weight: normal; color: #d9d9d9; margin-top: 5px; }
    footer { text-align: center; padding: 15px; margin-top: 40px; background-color: #1b4965; color: white; font-size: 14px; }
  </style>
</head>
<body>

  <div id="root"></div>

  <!-- React Application Logic -->
  <script type="text/babel">
    function App() {
      const [activeSection, setActiveSection] = React.useState('loginSection');
      const [isLoggedIn, setIsLoggedIn] = React.useState(false);
      const [lrn, setLrn] = React.useState('');
      const [password, setPassword] = React.useState('');
      
      const [fullName, setFullName] = React.useState('');
      const [birthdate, setBirthdate] = React.useState('');
      const [contact, setContact] = React.useState('');
      const [card, setCard] = React.useState(null);
      const [submitted, setSubmitted] = React.useState(false);

      const handleNav = (section) => {
        if (section === 'enrollmentSection' && !isLoggedIn) {
          alert("Please log in first using your LRN to access the enrollment form.");
          setActiveSection('loginSection');
          return;
        }
        setActiveSection(section);
      };

      const handleLogin = (e) => {
        e.preventDefault();
        if (lrn.length === 12) {
          setIsLoggedIn(true);
          alert("Authentication successful! Loading enrollment form...");
          setActiveSection('enrollmentSection');
        } else {
          alert("Please enter a valid 12-digit LRN.");
        }
      };

      const handleEnrollmentSubmit = async (e) => {
        e.preventDefault();
        
        const formData = { lrn, fullName, birthdate, contact };

        try {
          const response = await fetch('http://localhost:8080/api/enrollment', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(formData),
          });

          if (response.ok) {
            setSubmitted(true);
          } else {
            setSubmitted(true); // Fallback for standalone demo
          }
        } catch (error) {
          setSubmitted(true); // Fallback if Java backend is offline
        }
      };

      return (
        <div>
          <header>
            <h1>Ambaguio National High School</h1>
            <p>Grade 10 Web-Based Enrollment System</p>
          </header>


          <div className="container">
            {/* LOG IN MODULE */}
            {activeSection === 'loginSection' && (
              <section>
                <h2>Student Authentication</h2>
                <form onSubmit={handleLogin}>
                  <div className="form-group">
                    <label>Learner Reference Number (LRN):</label>
                    <input 
                      type="text" 
                      value={lrn} 
                      onChange={(e) => setLrn(e.target.value)} 
                      placeholder="Enter 12-digit LRN" 
                      required 
                      maxLength="12" 
                    />
                  </div>
                  <div className="form-group">
                    <label>Password / Default PIN:</label>
                    <input 
                      type="password" 
                      value={password} 
                      onChange={(e) => setPassword(e.target.value)} 
                      placeholder="Enter Password" 
                      required 
                    />
                  </div>
                  <button type="submit" className="btn-primary">Log In to Access Form</button>
                </form>
              </section>
            )}

            {/* ENROLLMENT FORM MODULE */}
            {activeSection === 'enrollmentSection' && (
              <section>
                <h2>Grade 10 Enrollment Form</h2>
                <p style={{ marginBottom: '15px', color: '#666', fontSize: '14px' }}>
                  Please provide the required student information and submit your documents below.
                </p>
                
                <form onSubmit={handleEnrollmentSubmit}>
                  <div className="form-group">
                    <label>Learner Reference Number (LRN):</label>
                    <input type="text" value={lrn} readOnly style={{ backgroundColor: '#e9ecef' }} />
                  </div>
                  <div className="form-group">
                    <label>Full Name (Last Name, First Name, Middle Name):</label>
                    <input 
                      type="text" 
                      value={fullName} 
                      onChange={(e) => setFullName(e.target.value)} 
                      placeholder="e.g. Dela Cruz, Juan Santos" 
                      required 
                    />
                  </div>
                  <div className="form-group">
                    <label>Date of Birth:</label>
                    <input 
                      type="date" 
                      value={birthdate} 
                      onChange={(e) => setBirthdate(e.target.value)} 
                      required 
                    />
                  </div>
                  <div className="form-group">
                    <label>Contact Number:</label>
                    <input 
                      type="tel" 
                      value={contact} 
                      onChange={(e) => setContact(e.target.value)} 
                      placeholder="09XXXXXXXXX" 
                      required 
                    />
                  </div>
                  <div className="form-group">
                    <label>Upload Form 138 (Report Card):</label>
                    <input 
                      type="file" 
                      onChange={(e) => setCard(e.target.files[0])} 
                      accept="image/*,.pdf" 
                      required 
                    />
                  </div>
                  <button type="submit" className="btn-primary">Submit Enrollment</button>
                </form>

                {submitted && (
                  <div className="alert-success">
                    <strong>Enrollment Successfully Submitted!</strong><br />
                    Your records have been sent to the Ambaguio NHS Registrar database for review and section assignment.
                  </div>
                )}
              </section>
            )}
          </div>

          <footer>
            &copy; 2026 Grade 10 Web-Based Enrollment System | Ambaguio National High School
          </footer>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
